---
title: .NET istemcisiyle gRPC hizmetlerini çağırma
author: jamesnk
description: .NET gRPC istemcisiyle gRPC hizmetlerini çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: c554ce9702a9f2b2efeabbfdf0d1319ca4161a1c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774736"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET istemcisiyle gRPC hizmetlerini çağırma

[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet paketinde bir .net GRPC istemci kitaplığı mevcuttur. Bu belgede nasıl yapılacağı açıklanmaktadır:

* GRPC istemcisini, gRPC hizmetlerini çağırmak üzere yapılandırın.
* GRPC 'yi birli, sunucu akışı, istemci akışı ve iki yönlü akış yöntemlerine göre çağırır.

## <a name="configure-grpc-client"></a>GRPC istemcisini yapılandırma

GRPC istemcileri [ * \*. proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut GRPC istemcisinde * \*. proto* dosyasındaki GRPC hizmetine çeviren yöntemler vardır.

Bir kanaldan gRPC istemcisi oluşturulur. ' I kullanarak `GrpcChannel.ForAddress` bir kanal oluşturun ve ardından bir GRPC istemcisi oluşturmak için kanalı kullanın:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanal, gRPC hizmetine uzun süreli bir bağlantıyı temsil eder. Bir kanal oluşturulduğunda, hizmet çağırma ile ilgili seçeneklerle yapılandırılır. Örneğin, çağrı yapmak `HttpClient` için kullanılan, en fazla ileti ve alma iletisi boyutu, ve ' de üzerinde `GrpcChannelOptions` günlüğe kaydetme belirlenebilir. `GrpcChannel.ForAddress` Seçeneklerin tamamı listesi için bkz. [istemci yapılandırma seçenekleri](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Kanal ve istemci performansı ve kullanımı:

* Kanal oluşturma maliyetli bir işlem olabilir. GRPC çağrıları için bir kanalı yeniden kullanmak performans avantajları sağlar.
* gRPC istemcileri kanallarla oluşturulur. gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.
* Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.
* Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.
* Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.

`GrpcChannel.ForAddress`gRPC istemcisi oluşturmak için tek seçenek değildir. GRPC hizmetlerini bir ASP.NET Core uygulamasından çağırmak için, [GRPC istemci fabrikası tümleştirmesini](xref:grpc/clientfactory)göz önünde bulundurun. ile gRPC tümleştirmesi `HttpClientFactory` , GRPC istemcileri oluşturmaya yönelik merkezi bir alternatif sunar.

> [!NOTE]
> [.Net istemcisiyle güvenli olmayan gRPC hizmetlerini çağırmak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gerekir.

> [!NOTE]
> İle `Grpc.Net.Client` http/2 üzerinden GRPC çağırma, Xamarin üzerinde şu anda desteklenmiyor. Gelecek bir Xamarin sürümünde HTTP/2 desteğini geliştirmek için çalışıyoruz. [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) ve [GRPC-Web](xref:grpc/browser) , bugün çalışan önemli alternatiflerdir.

## <a name="make-grpc-calls"></a>GRPC çağrıları yapma

Bir gRPC çağrısı, istemcideki bir yöntem çağırarak başlatılır. GRPC istemcisi, ileti serileştirme işlemini işleyecek ve doğru hizmete gRPC çağrısını ele alacak.

gRPC farklı türde yöntemlere sahiptir. İstemcisinin bir gRPC çağrısını yapmak için nasıl kullanıldığı, çağrılan yöntemin türüne bağlıdır. GRPC Yöntem türleri şunlardır:

* Li
* Sunucu akışı
* İstemci akışı
* İki yönlü akış

### <a name="unary-call"></a>Birli çağrı

Birli çağrı, istemci isteği iletisi gönderen ile başlar. Hizmet bittiğinde bir yanıt iletisi döndürülür.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

. Proto dosyasındaki her birli hizmet yöntemi, yöntemi çağırmak için somut GRPC istemci türünde iki .NET yöntemi oluşmasına neden olur: zaman uyumsuz bir yöntem ve engelleyici bir yöntem. * \** Örneğin, iki çağırma `GreeterClient` `SayHello`yöntemi vardır:

* `GreeterClient.SayHelloAsync`-hizmeti `Greeter.SayHello` zaman uyumsuz olarak çağırır. Beklenmiş olabilir.
* `GreeterClient.SayHello`-tamamlanana `Greeter.SayHello` kadar hizmeti ve blokları çağırır. Zaman uyumsuz kodda kullanmayın.

### <a name="server-streaming-call"></a>Sunucu akışı çağrısı

Sunucu akış çağrısı, istemci isteği iletisi gönderen ile başlar. `ResponseStream.MoveNext()`hizmetten akan iletileri okur. ' İ `ResponseStream.MoveNext()` döndüğünde `false`sunucu akış çağrısı tamamlanmıştır.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir. `IAsyncStreamReader<T>.ReadAllAsync()` Uzantı yöntemi, yanıt akışından gelen tüm iletileri okur:

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

İstemci akış *çağrısı, istemci* İleti göndermeden başlatılır. İstemci, ile `RequestStream.WriteAsync`ileti göndermek için seçim yapabilir. İstemci ileti göndermeyi bitirdiğinde, `RequestStream.CompleteAsync` hizmete bildirmek için çağrılmalıdır. Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.

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

### <a name="bi-directional-streaming-call"></a>İki yönlü akış çağrısı

Çift yönlü bir akış *çağrısı, istemci* bir ileti göndermeden başlatılır. İstemci, ile `RequestStream.WriteAsync`ileti göndermek için seçim yapabilir. Hizmetten akışa alınan iletilere veya `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`ile erişilebilir. İki yönlü akış çağrısı, `ResponseStream` daha fazla ileti olmadığında tamamlanır.

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

Çift yönlü bir akış araması sırasında, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir. Çift yönlü bir çağrı ile etkileşimde bulunmak için en iyi istemci mantığı, hizmet mantığına bağlı olarak farklılık gösterir.

## <a name="access-grpc-trailers"></a>GRPC tanıtımlarına erişim

gRPC çağrıları gRPC fragme döndürebilir. gRPC fragmanları, bir çağrı hakkında ad/değer meta verileri sağlamak için kullanılır. Treylik, HTTP üst bilgilerine benzer işlevler sağlar, ancak çağrının sonunda alınır.

gRPC tanıtımlarına kullanılarak `GetTrailers()`erişilebilir ve bu, meta veri koleksiyonu döndürür. Yanıt tamamlandıktan sonra tanıtımları döndürülür, bu nedenle, tanıtıma erişmeden önce tüm yanıt iletilerini beklemelidir.

Tek birli ve istemci akış çağrıları çağrılmadan `ResponseAsync` `GetTrailers()`önce await olmalıdır:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

Sunucu ve çift yönlü akış çağrılarının, çağrılmadan `GetTrailers()`önce yanıt akışını beklemesi gerekir:

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

gRPC tanıtımlarına da adresinden `RpcException`erişilebilir. Bir hizmet, tamam olmayan bir gRPC durumuyla birlikte Treyi döndürebilir. Bu durumda, daha sonra gRPC istemcisi tarafından oluşturulan özel durumdan tanıtımları alınır:

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
