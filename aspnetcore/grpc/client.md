---
title: .NET istemcisi ile gRPC hizmetlerini arayın
author: jamesnk
description: .NET gRPC istemcisi ile gRPC hizmetlerini nasıl arayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667176"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET istemcisi ile gRPC hizmetlerini arayın

Bir .NET gRPC istemci kitaplığı [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet paketinde mevcuttur. Bu belge, nasıl açıklanmaktadır:

* gRPC hizmetlerini aramak için bir gRPC istemcisini yapılandırın.
* Unary, sunucu akışı, istemci akışı ve çift yönlü akış yöntemleri için gRPC aramaları yapın.

## <a name="configure-grpc-client"></a>gRPC istemcisi yapılandırma

gRPC istemcileri [ * \*.proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut gRPC istemcisi * \*.proto* dosyasındaki gRPC hizmetine çevrilen yöntemlere sahiptir.

Bir gRPC istemcisi bir kanaldan oluşturulur. Bir kanal `GrpcChannel.ForAddress` oluşturmak için kullanarak başlayın ve sonra bir gRPC istemcisi oluşturmak için kanalı kullanın:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanal, gRPC hizmetiyle uzun süreli bir bağlantıyı temsil eder. Bir kanal oluşturulduğunda, bir hizmeti çağırmayla ilgili seçeneklerle yapılandırılır. Örneğin, arama `HttpClient` yapmak için kullanılan, en fazla gönder ve ileti boyutu `GrpcChannelOptions` ve `GrpcChannel.ForAddress`günlük belirtilebilir ve . Seçeneklerin tam listesi için [istemci yapılandırma seçeneklerine](xref:grpc/configuration#configure-client-options)bakın.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Kanal ve istemci performansı ve kullanımı:

* Kanal oluşturmak pahalı bir işlem olabilir. gRPC aramaları için bir kanalı yeniden kullanmak performans avantajları sağlar.
* gRPC istemcileri kanallarla oluşturulur. gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.
* Farklı istemci türleri de dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.
* Kanaldan oluşturulan bir kanal ve istemciler birden çok iş parçacığı tarafından güvenle kullanılabilir.
* Kanaldan oluşturulan istemciler birden çok eşzamanlı arama yapabilir.

`GrpcChannel.ForAddress`gRPC istemcisi oluşturmak için tek seçenek değildir. bir ASP.NET Core uygulamasından gRPC hizmetlerini arıyorsanız, [gRPC istemci fabrika tümleştirmesi](xref:grpc/clientfactory)göz önünde bulundurun. gRPC istemcileri oluşturmak için merkezi bir alternatif sunar. `HttpClientFactory`

> [!NOTE]
> [.NET istemcisi ile güvenli olmayan gRPC hizmetlerini aramak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gereklidir.

> [!NOTE]
> HTTP/2 üzerinden `Grpc.Net.Client` gRPC arama şu anda Xamarin desteklenmez. Biz gelecekteki bir Xamarin sürümünde HTTP / 2 desteği geliştirmek için çalışıyoruz. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) ve [gRPC-Web](xref:grpc/browser) bugün çalışmak uygun alternatifler vardır.

## <a name="make-grpc-calls"></a>gRPC aramaları yapma

Bir gRPC arama istemciüzerinde bir yöntem çağırarak başlatılır. gRPC istemcisi ileti serileştirme ve doğru hizmet için gRPC arama adresleme işleyecek.

gRPC'nin farklı yöntemleri vardır. GRPC araması yapmak için istemciyi nasıl kullanacağınız aradığınız yöntemin türüne bağlıdır. gRPC yöntem türleri şunlardır:

* Tekli
* Sunucu akışı
* İstemci akışı
* Çift yönlü akış

### <a name="unary-call"></a>Unary çağrı

Unary arama istemcibir istek iletisi gönderme ile başlar. Hizmet bittiğinde bir yanıt iletisi döndürülür.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

* \*.proto* dosyasındaki her bir unary servis yöntemi, metodu çağırmak için beton gRPC istemci türünde iki .NET yöntemiyle sonuçlanır: bir senkron yöntem ve engelleme yöntemi. Örneğin, arama `GreeterClient` nın iki yolu `SayHello`vardır:

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` hizmeti eş zamanlı olarak çağırır. Beklenebilir.
* `GreeterClient.SayHello`- `Greeter.SayHello` tamamlanana kadar servis ve bloklar çağırır. Eşzamanlı kod kullanmayın.

### <a name="server-streaming-call"></a>Sunucu akış çağrısı

Sunucu akışı çağrısı istemcinin istek iletisi göndermesiyle başlar. `ResponseStream.MoveNext()`hizmetten aktarılan iletileri okur. Sunucu akışı çağrısı döndürdüğünde `ResponseStream.MoveNext()` `false`tamamlanır.

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

C# 8 veya daha sonra `await foreach` kullanıyorsanız, sözdizimi iletileri okumak için kullanılabilir. Uzantı `IAsyncStreamReader<T>.ReadAllAsync()` yöntemi yanıt akışındaki tüm iletileri okur:

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>İstemci akışı çağrısı

İstemci akışı çağrısı, istemci ileti *göndermeden* başlar. İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir. İstemci ileti göndermeyi `RequestStream.CompleteAsync` bitirdiğinde hizmeti bildirmek için çağrılmalıdır. Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>Çift yönlü akış çağrısı

İstemci ileti *göndermeden* çift yönlü akış çağrısı başlar. İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir. Hizmetten aktarılan iletilere veya `ResponseStream.MoveNext()` . `ResponseStream.ReadAllAsync()` Daha fazla ileti olmadığında `ResponseStream` çift yönlü akış çağrısı tamamlanır.

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

Çift yönlü akış araması sırasında, istemci ve hizmet istediği zaman birbirlerine ileti gönderebilir. Çift yönlü bir çağrıyla etkileşim kurmak için en iyi istemci mantığı hizmet mantığına bağlı olarak değişir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
