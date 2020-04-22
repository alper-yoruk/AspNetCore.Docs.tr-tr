---
title: .NET istemcisiyle gRPC hizmetlerini çağırma
author: jamesnk
description: .NET gRPC istemcisi ile gRPC hizmetlerini nasıl arayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
uid: grpc/client
ms.openlocfilehash: aefa52a5c4c66178c5978aebd4cd9b00559c7f54
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791556"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET istemcisiyle gRPC hizmetlerini çağırma

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

`GrpcChannel.ForAddress`gRPC istemcisi oluşturmak için tek seçenek değildir. Bir ASP.NET Core uygulamasından gRPC hizmetlerini arıyorsanız, [gRPC istemci fabrika tümleştirmesi](xref:grpc/clientfactory)göz önünde bulundurun. gRPC istemcileri oluşturmak için merkezi bir alternatif sunar. `HttpClientFactory`

> [!NOTE]
> [.NET istemcisi ile güvenli olmayan gRPC hizmetlerini aramak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gereklidir.

> [!NOTE]
> HTTP/2 üzerinden `Grpc.Net.Client` gRPC arama şu anda Xamarin desteklenmez. Biz gelecekteki bir Xamarin sürümünde HTTP / 2 desteği geliştirmek için çalışıyoruz. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) ve [gRPC-Web](xref:grpc/browser) bugün çalışmak uygun alternatifler vardır.

## <a name="make-grpc-calls"></a>gRPC aramaları yapma

Bir gRPC arama istemciüzerinde bir yöntem çağırarak başlatılır. gRPC istemcisi ileti serileştirme ve doğru hizmet için gRPC arama adresleme işleyecek.

gRPC'nin farklı yöntemleri vardır. İstemcinin gRPC araması yapmak için nasıl kullanıldığı, çağrılan yöntemin türüne bağlıdır. gRPC yöntem türleri şunlardır:

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
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

C# 8 veya daha `await foreach` sonra kullanırken, sözdizimi iletileri okumak için kullanılabilir. Uzantı `IAsyncStreamReader<T>.ReadAllAsync()` yöntemi yanıt akışındaki tüm iletileri okur:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>İstemci akışı çağrısı

İstemci akışı çağrısı, istemci ileti *göndermeden* başlar. İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir. İstemci ileti göndermeyi bitirdiğinde, `RequestStream.CompleteAsync` hizmeti bildirmek için çağrılmalıdır. Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a>Çift yönlü akış çağrısı

İstemci ileti *göndermeden* çift yönlü akış çağrısı başlar. İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir. Hizmetten aktarılan iletilere veya `ResponseStream.MoveNext()` . `ResponseStream.ReadAllAsync()` Daha fazla ileti olmadığında `ResponseStream` çift yönlü akış çağrısı tamamlanır.

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

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
```

Çift yönlü akış araması sırasında, istemci ve hizmet istediği zaman birbirlerine ileti gönderebilir. Çift yönlü bir çağrıyla etkileşim kurmak için en iyi istemci mantığı hizmet mantığına bağlı olarak değişir.

## <a name="access-grpc-trailers"></a>gRPC fragmanlarına erişin

gRPC aramaları gRPC römorkları döndürebilir. gRPC römorkları, bir çağrı yla ilgili ad/değer meta verilerini sağlamak için kullanılır. Römorklar HTTP üstbilgilerine benzer işlevler sağlar, ancak aramanın sonunda alınır.

gRPC römorklar kullanılarak `GetTrailers()`erişilebilir , hangi meta veri bir koleksiyon döndürür. Yanıt tamamlandıktan sonra römorklar döndürülür, bu nedenle, römorklara erişmeden önce tüm yanıt iletilerini beklemeniz gerekir.

Unary ve istemci akışı `ResponseAsync` aramaları `GetTrailers()`aramadan önce beklemesi gerekir:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

Sunucu ve çift yönlü akış çağrıları aramadan `GetTrailers()`önce yanıt akışını beklerken bitirmek gerekir:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

gRPC römorklar da `RpcException`erişilebilir . Bir hizmet, tamam olmayan bir gRPC durumuyla birlikte römorkları döndürebilir. Bu durumda römorklar gRPC istemcisi tarafından atılan özel durum alınır:

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
