---
title: .NET istemcisiyle gRPC hizmetlerini çağırma
author: jamesnk
description: .NET gRPC istemcisiyle gRPC hizmetlerini çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699136"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET istemcisiyle gRPC hizmetlerini çağırma

[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet paketinde bir .net GRPC istemci kitaplığı mevcuttur. Bu belgede nasıl yapılacağı açıklanmaktadır:

* GRPC istemcisini, gRPC hizmetlerini çağırmak üzere yapılandırın.
* GRPC 'yi birli, sunucu akışı, istemci akışı ve iki yönlü akış yöntemlerine göre çağırır.

## <a name="configure-grpc-client"></a>GRPC istemcisini yapılandırma

gRPC istemcileri [ *\* . proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut gRPC istemcisinde *\* . proto* dosyasındaki GRPC hizmetine çeviren yöntemler vardır. Örneğin, adlı bir hizmet `Greeter` `GreeterClient` hizmeti çağırmak için yöntemler içeren bir tür üretir.

Bir kanaldan gRPC istemcisi oluşturulur. `GrpcChannel.ForAddress`' I kullanarak bir kanal oluşturun ve ardından bir gRPC istemcisi oluşturmak için kanalı kullanın:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanal, gRPC hizmetine uzun süreli bir bağlantıyı temsil eder. Bir kanal oluşturulduğunda, hizmet çağırma ile ilgili seçeneklerle yapılandırılır. Örneğin, `HttpClient` çağrı yapmak için kullanılan, en fazla ileti ve alma iletisi boyutu, ve ' de üzerinde günlüğe kaydetme belirlenebilir `GrpcChannelOptions` `GrpcChannel.ForAddress` . Seçeneklerin tamamı listesi için bkz. [istemci yapılandırma seçenekleri](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>TLS’yi yapılandırma

Bir gRPC istemcisinin, çağrılan hizmetle aynı bağlantı düzeyi güvenliği kullanması gerekir. GRPC istemci Aktarım Katmanı Güvenliği (TLS), gRPC kanalı oluşturulduğunda yapılandırılır. Bir gRPC istemcisi, bir hizmet çağırdığında bir hata oluşturur ve kanalın ve hizmetin bağlantı düzeyi güvenliği eşleşmez.

TLS kullanmak için bir gRPC kanalını yapılandırmak üzere sunucu adresinin ile başladığı emin olun `https` . Örneğin, `GrpcChannel.ForAddress("https://localhost:5001")` https protokolünü kullanır. GRPC kanalı, TLS ile güvenliği sağlanmış bir bağlantıyı otomatik olarak belirler ve gRPC çağrıları yapmak için güvenli bir bağlantı kullanır.

> [!TIP]
> gRPC, TLS üzerinden istemci sertifikası kimlik doğrulamasını destekler. Bir gRPC kanalı ile istemci sertifikalarını yapılandırma hakkında bilgi için bkz <xref:grpc/authn-and-authz#client-certificate-authentication> ..

Güvenli olmayan gRPC hizmetlerini çağırmak için sunucu adresinin ile başladığı emin olun `http` . Örneğin, `GrpcChannel.ForAddress("http://localhost:5000")` http protokolünü kullanır. .NET Core 3,1 veya üzeri sürümlerde, [.net istemcisiyle güvenli olmayan gRPC hizmetlerini çağırmak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gerekir.

### <a name="client-performance"></a>İstemci performansı

Kanal ve istemci performansı ve kullanımı:

* Kanal oluşturma maliyetli bir işlem olabilir. GRPC çağrıları için bir kanalı yeniden kullanmak performans avantajları sağlar.
* gRPC istemcileri kanallarla oluşturulur. gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.
* Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.
* Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.
* Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.

`GrpcChannel.ForAddress` gRPC istemcisi oluşturmak için tek seçenek değildir. GRPC hizmetlerini bir ASP.NET Core uygulamasından çağırmak için, [GRPC istemci fabrikası tümleştirmesini](xref:grpc/clientfactory)göz önünde bulundurun. ile gRPC tümleştirmesi, `HttpClientFactory` GRPC istemcileri oluşturmaya yönelik merkezi bir alternatif sunar.

> [!NOTE]
> İle HTTP/2 üzerinden gRPC çağırma `Grpc.Net.Client` , Xamarin üzerinde şu anda desteklenmiyor. Gelecek bir Xamarin sürümünde HTTP/2 desteğini geliştirmek için çalışıyoruz. [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) ve [GRPC-Web](xref:grpc/browser) , bugün çalışan önemli alternatiflerdir.

## <a name="make-grpc-calls"></a>GRPC çağrıları yapma

Bir gRPC çağrısı, istemcideki bir yöntem çağırarak başlatılır. GRPC istemcisi, ileti serileştirme işlemini işleyecek ve doğru hizmete gRPC çağrısını ele alacak.

gRPC farklı türde yöntemlere sahiptir. İstemcisinin bir gRPC çağrısını yapmak için nasıl kullanıldığı, çağrılan yöntemin türüne bağlıdır. GRPC Yöntem türleri şunlardır:

* Birli
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

*\* . Proto* dosyasındaki her birli hizmet yöntemi, yöntemi çağırmak Için somut GRPC istemci türünde iki .NET yöntemi oluşmasına neden olur: zaman uyumsuz bir yöntem ve engelleyici bir yöntem. Örneğin, `GreeterClient` iki çağırma yöntemi vardır `SayHello` :

* `GreeterClient.SayHelloAsync` - `Greeter.SayHello` hizmeti zaman uyumsuz olarak çağırır. Beklenmiş olabilir.
* `GreeterClient.SayHello` - `Greeter.SayHello` tamamlanana kadar hizmeti ve blokları çağırır. Zaman uyumsuz kodda kullanmayın.

### <a name="server-streaming-call"></a>Sunucu akışı çağrısı

Sunucu akış çağrısı, istemci isteği iletisi gönderen ile başlar. `ResponseStream.MoveNext()` hizmetten akan iletileri okur. ' İ döndüğünde sunucu akış çağrısı tamamlanmıştır `ResponseStream.MoveNext()` `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir. `IAsyncStreamReader<T>.ReadAllAsync()`Uzantı yöntemi, yanıt akışından gelen tüm iletileri okur:

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

İstemci akış *çağrısı, istemci* İleti göndermeden başlatılır. İstemci, ile ileti göndermek için seçim yapabilir `RequestStream.WriteAsync` . İstemci ileti göndermeyi bitirdiğinde, `RequestStream.CompleteAsync()` hizmete bildirmek için çağrılmalıdır. Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.

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

Çift yönlü bir akış *çağrısı, istemci* bir ileti göndermeden başlatılır. İstemci, ile ileti göndermek için seçim yapabilir `RequestStream.WriteAsync` . Hizmetten akışa alınan iletilere veya ile erişilebilir `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()` . İki yönlü akış çağrısı, `ResponseStream` daha fazla ileti olmadığında tamamlanır.

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

En iyi performans için ve istemci ve hizmette gereksiz hatalardan kaçınmak için çift yönlü akış çağrılarını sorunsuz bir şekilde tamamlamayı deneyin. Sunucu istek akışını okumayı bitirdiğinde ve istemci yanıt akışını okumayı bitirdiğinde, çift yönlü bir çağrı düzgün bir şekilde tamamlanır. Yukarıdaki örnek çağrı, düzgün bir şekilde biten iki yönlü çağrının bir örneğidir. Çağrısında istemcisi:

1. ' I çağırarak yeni bir çift yönlü akış çağrısı başlatır `EchoClient.Echo` .
2. Hizmetini kullanarak hizmetten iletileri okumak için bir arka plan görevi oluşturur `ResponseStream.ReadAllAsync()` .
3. Sunucusuna iletileri gönderir `RequestStream.WriteAsync` .
4. Sunucuya ileti göndermeyi bitirmesinin tamamlandığını bildirir `RequestStream.CompleteAsync()` .
5. Arka plan görevi tüm gelen iletileri okuuncaya kadar bekler.

Çift yönlü bir akış araması sırasında, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir. Çift yönlü bir çağrı ile etkileşimde bulunmak için en iyi istemci mantığı, hizmet mantığına bağlı olarak farklılık gösterir.

## <a name="access-grpc-headers"></a>GRPC üst bilgilerine erişin

gRPC çağrıları yanıt üst bilgilerini döndürür. HTTP yanıt üstbilgileri döndürülen iletiyle ilgili olmayan bir çağrı hakkında ad/değer meta verilerini iletir.

Üst bilgileri kullanılarak erişilebilir `ResponseHeadersAsync` ve bu, meta veri koleksiyonu döndürür. Üstbilgiler genellikle yanıt iletisiyle birlikte döndürülür; Bu nedenle, onları beklemelidir.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

`ResponseHeadersAsync` kullanımıyla

* , `ResponseHeadersAsync` Üst bilgi toplamayı almak için sonucunu beklemelidir.
* Öğesinden önce erişilmesi gerekmez `ResponseAsync` (veya akış sırasında yanıt akışı). Yanıt döndürülürse, `ResponseHeadersAsync` üstbilgileri anında geri döndürür.
* , Bir bağlantı veya sunucu hatası varsa ve gRPC çağrısı için üst bilgiler döndürülmediğinde bir özel durum oluşturur.

## <a name="access-grpc-trailers"></a>GRPC tanıtımlarına erişim

gRPC çağrıları, yanıt fragmanları döndürebilir. Treyi, bir çağrı hakkında ad/değer meta verileri sağlamak için kullanılır. Treylik, HTTP üst bilgilerine benzer işlevler sağlar, ancak çağrının sonunda alınır.

Treyler `GetTrailers()` , meta veri koleksiyonu döndüren kullanılarak erişilebilir. Yanıt tamamlandıktan sonra Treyi döndürülür. Bu nedenle, treyleri erişmeden önce tüm yanıt iletilerini beklemelidir.

Tek birli ve istemci akış çağrıları `ResponseAsync` çağrılmadan önce await olmalıdır `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Sunucu ve çift yönlü akış çağrılarının, çağrılmadan önce yanıt akışını beklemesi gerekir `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Tanıtımlardan de adresinden erişilebilir `RpcException` . Bir hizmet, tamam olmayan bir gRPC durumuyla birlikte Treyi döndürebilir. Bu durumda, tanıtımları gRPC istemcisi tarafından oluşturulan özel durumdan alınmıştır:

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
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a>Son tarihi Yapılandır

Bir çağrının ne kadar süreyle çalıştırılabildiğinden bir üst sınır sağladığından bir gRPC çağrı son tarihinin yapılandırılması önerilir. Yanlış çalışan hizmetlerin sürekli olarak çalıştırılmasını ve sunucu kaynaklarının tüketilmesi işlemini sonlandırır. Son tarihler, güvenilir uygulamalar oluşturmaya yönelik yararlı bir araçtır.

`CallOptions.Deadline`Bir gRPC çağrısının son tarihini ayarlamak için yapılandırın:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Daha fazla bilgi için bkz. <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
