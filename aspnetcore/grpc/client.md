---
title: .NET istemcisiyle gRPC hizmetlerini çağırma
author: jamesnk
description: .NET gRPC istemcisiyle gRPC hizmetlerini çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
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
ms.openlocfilehash: 9406c2a34126f3e5cd1406a55c3585e7a28f3dd9
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90593053"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="5d326-103">.NET istemcisiyle gRPC hizmetlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="5d326-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="5d326-104">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet paketinde bir .net GRPC istemci kitaplığı mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="5d326-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="5d326-105">Bu belgede nasıl yapılacağı açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="5d326-105">This document explains how to:</span></span>

* <span data-ttu-id="5d326-106">GRPC istemcisini, gRPC hizmetlerini çağırmak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5d326-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="5d326-107">GRPC 'yi birli, sunucu akışı, istemci akışı ve iki yönlü akış yöntemlerine göre çağırır.</span><span class="sxs-lookup"><span data-stu-id="5d326-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="5d326-108">GRPC istemcisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5d326-108">Configure gRPC client</span></span>

<span data-ttu-id="5d326-109">gRPC istemcileri [ \* \* . proto\* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir.</span><span class="sxs-lookup"><span data-stu-id="5d326-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="5d326-110">Somut gRPC istemcisinde \* \* . proto\* dosyasındaki GRPC hizmetine çeviren yöntemler vardır.</span><span class="sxs-lookup"><span data-stu-id="5d326-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="5d326-111">Bir kanaldan gRPC istemcisi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5d326-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="5d326-112">`GrpcChannel.ForAddress`' I kullanarak bir kanal oluşturun ve ardından bir gRPC istemcisi oluşturmak için kanalı kullanın:</span><span class="sxs-lookup"><span data-stu-id="5d326-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="5d326-113">Kanal, gRPC hizmetine uzun süreli bir bağlantıyı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5d326-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="5d326-114">Bir kanal oluşturulduğunda, hizmet çağırma ile ilgili seçeneklerle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="5d326-115">Örneğin, `HttpClient` çağrı yapmak için kullanılan, en fazla ileti ve alma iletisi boyutu, ve ' de üzerinde günlüğe kaydetme belirlenebilir `GrpcChannelOptions` `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="5d326-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="5d326-116">Seçeneklerin tamamı listesi için bkz. [istemci yapılandırma seçenekleri](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="5d326-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="5d326-117">TLS’yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5d326-117">Configure TLS</span></span>

<span data-ttu-id="5d326-118">Bir gRPC istemcisinin, çağrılan hizmetle aynı bağlantı düzeyi güvenliği kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5d326-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="5d326-119">GRPC istemci Aktarım Katmanı Güvenliği (TLS), gRPC kanalı oluşturulduğunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="5d326-120">Bir gRPC istemcisi, bir hizmet çağırdığında bir hata oluşturur ve kanalın ve hizmetin bağlantı düzeyi güvenliği eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="5d326-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="5d326-121">TLS kullanmak için bir gRPC kanalını yapılandırmak üzere sunucu adresinin ile başladığı emin olun `https` .</span><span class="sxs-lookup"><span data-stu-id="5d326-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="5d326-122">Örneğin, `GrpcChannel.ForAddress("https://localhost:5001")` https protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="5d326-123">GRPC kanalı, TLS ile güvenliği sağlanmış bir bağlantıyı otomatik olarak alır ve gRPC çağrıları yapmak için güvenli bir bağlantı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="5d326-124">gRPC, TLS üzerinden istemci sertifikası kimlik doğrulamasını destekler.</span><span class="sxs-lookup"><span data-stu-id="5d326-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="5d326-125">Bir gRPC kanalı ile istemci sertifikalarını yapılandırma hakkında bilgi için bkz <xref:grpc/authn-and-authz#client-certificate-authentication> ..</span><span class="sxs-lookup"><span data-stu-id="5d326-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="5d326-126">Güvenli olmayan gRPC hizmetlerini çağırmak için sunucu adresinin ile başladığı emin olun `http` .</span><span class="sxs-lookup"><span data-stu-id="5d326-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="5d326-127">Örneğin, `GrpcChannel.ForAddress("http://localhost:5000")` http protokolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="5d326-128">.NET Core 3,1 veya üzeri sürümlerde, [.net istemcisiyle güvenli olmayan gRPC hizmetlerini çağırmak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="5d326-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="5d326-129">İstemci performansı</span><span class="sxs-lookup"><span data-stu-id="5d326-129">Client performance</span></span>

<span data-ttu-id="5d326-130">Kanal ve istemci performansı ve kullanımı:</span><span class="sxs-lookup"><span data-stu-id="5d326-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="5d326-131">Kanal oluşturma maliyetli bir işlem olabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="5d326-132">GRPC çağrıları için bir kanalı yeniden kullanmak performans avantajları sağlar.</span><span class="sxs-lookup"><span data-stu-id="5d326-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="5d326-133">gRPC istemcileri kanallarla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5d326-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="5d326-134">gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5d326-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="5d326-135">Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="5d326-136">Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="5d326-137">Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="5d326-138">`GrpcChannel.ForAddress` gRPC istemcisi oluşturmak için tek seçenek değildir.</span><span class="sxs-lookup"><span data-stu-id="5d326-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="5d326-139">GRPC hizmetlerini bir ASP.NET Core uygulamasından çağırmak için, [GRPC istemci fabrikası tümleştirmesini](xref:grpc/clientfactory)göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="5d326-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="5d326-140">ile gRPC tümleştirmesi, `HttpClientFactory` GRPC istemcileri oluşturmaya yönelik merkezi bir alternatif sunar.</span><span class="sxs-lookup"><span data-stu-id="5d326-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="5d326-141">İle HTTP/2 üzerinden gRPC çağırma `Grpc.Net.Client` , Xamarin üzerinde şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="5d326-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="5d326-142">Gelecek bir Xamarin sürümünde HTTP/2 desteğini geliştirmek için çalışıyoruz.</span><span class="sxs-lookup"><span data-stu-id="5d326-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="5d326-143">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) ve [GRPC-Web](xref:grpc/browser) , bugün çalışan önemli alternatiflerdir.</span><span class="sxs-lookup"><span data-stu-id="5d326-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="5d326-144">GRPC çağrıları yapma</span><span class="sxs-lookup"><span data-stu-id="5d326-144">Make gRPC calls</span></span>

<span data-ttu-id="5d326-145">Bir gRPC çağrısı, istemcideki bir yöntem çağırarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="5d326-146">GRPC istemcisi, ileti serileştirme işlemini işleyecek ve doğru hizmete gRPC çağrısını ele alacak.</span><span class="sxs-lookup"><span data-stu-id="5d326-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="5d326-147">gRPC farklı türde yöntemlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5d326-147">gRPC has different types of methods.</span></span> <span data-ttu-id="5d326-148">İstemcisinin bir gRPC çağrısını yapmak için nasıl kullanıldığı, çağrılan yöntemin türüne bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="5d326-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="5d326-149">GRPC Yöntem türleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="5d326-149">The gRPC method types are:</span></span>

* <span data-ttu-id="5d326-150">Birli</span><span class="sxs-lookup"><span data-stu-id="5d326-150">Unary</span></span>
* <span data-ttu-id="5d326-151">Sunucu akışı</span><span class="sxs-lookup"><span data-stu-id="5d326-151">Server streaming</span></span>
* <span data-ttu-id="5d326-152">İstemci akışı</span><span class="sxs-lookup"><span data-stu-id="5d326-152">Client streaming</span></span>
* <span data-ttu-id="5d326-153">İki yönlü akış</span><span class="sxs-lookup"><span data-stu-id="5d326-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="5d326-154">Birli çağrı</span><span class="sxs-lookup"><span data-stu-id="5d326-154">Unary call</span></span>

<span data-ttu-id="5d326-155">Birli çağrı, istemci isteği iletisi gönderen ile başlar.</span><span class="sxs-lookup"><span data-stu-id="5d326-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="5d326-156">Hizmet bittiğinde bir yanıt iletisi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="5d326-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="5d326-157">\* \* . Proto\* dosyasındaki her birli hizmet yöntemi, yöntemi çağırmak Için somut GRPC istemci türünde iki .NET yöntemi oluşmasına neden olur: zaman uyumsuz bir yöntem ve engelleyici bir yöntem.</span><span class="sxs-lookup"><span data-stu-id="5d326-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="5d326-158">Örneğin, `GreeterClient` iki çağırma yöntemi vardır `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="5d326-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="5d326-159">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` hizmeti zaman uyumsuz olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="5d326-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="5d326-160">Beklenmiş olabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-160">Can be awaited.</span></span>
* <span data-ttu-id="5d326-161">`GreeterClient.SayHello` - `Greeter.SayHello` tamamlanana kadar hizmeti ve blokları çağırır.</span><span class="sxs-lookup"><span data-stu-id="5d326-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="5d326-162">Zaman uyumsuz kodda kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5d326-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="5d326-163">Sunucu akışı çağrısı</span><span class="sxs-lookup"><span data-stu-id="5d326-163">Server streaming call</span></span>

<span data-ttu-id="5d326-164">Sunucu akış çağrısı, istemci isteği iletisi gönderen ile başlar.</span><span class="sxs-lookup"><span data-stu-id="5d326-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="5d326-165">`ResponseStream.MoveNext()` hizmetten akan iletileri okur.</span><span class="sxs-lookup"><span data-stu-id="5d326-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="5d326-166">' İ döndüğünde sunucu akış çağrısı tamamlanmıştır `ResponseStream.MoveNext()` `false` .</span><span class="sxs-lookup"><span data-stu-id="5d326-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="5d326-167">C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="5d326-168">`IAsyncStreamReader<T>.ReadAllAsync()`Uzantı yöntemi, yanıt akışından gelen tüm iletileri okur:</span><span class="sxs-lookup"><span data-stu-id="5d326-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="5d326-169">İstemci akışı çağrısı</span><span class="sxs-lookup"><span data-stu-id="5d326-169">Client streaming call</span></span>

<span data-ttu-id="5d326-170">İstemci akış *çağrısı, istemci* İleti göndermeden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="5d326-171">İstemci, ile ileti göndermek için seçim yapabilir `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="5d326-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="5d326-172">İstemci ileti göndermeyi bitirdiğinde, `RequestStream.CompleteAsync()` hizmete bildirmek için çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5d326-172">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="5d326-173">Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-173">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="5d326-174">İki yönlü akış çağrısı</span><span class="sxs-lookup"><span data-stu-id="5d326-174">Bi-directional streaming call</span></span>

<span data-ttu-id="5d326-175">Çift yönlü bir akış *çağrısı, istemci* bir ileti göndermeden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="5d326-176">İstemci, ile ileti göndermek için seçim yapabilir `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="5d326-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="5d326-177">Hizmetten akışa alınan iletilere veya ile erişilebilir `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="5d326-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="5d326-178">İki yönlü akış çağrısı, `ResponseStream` daha fazla ileti olmadığında tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="5d326-179">En iyi performans için ve istemci ve hizmette gereksiz hatalardan kaçınmak için çift yönlü akış çağrılarını sorunsuz bir şekilde tamamlamayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="5d326-179">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="5d326-180">Sunucu istek akışını okumayı bitirdiğinde ve istemci yanıt akışını okumayı bitirdiğinde, çift yönlü bir çağrı düzgün bir şekilde tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="5d326-180">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="5d326-181">Yukarıdaki örnek çağrı, düzgün bir şekilde biten iki yönlü çağrının bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="5d326-181">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="5d326-182">Çağrısında istemcisi:</span><span class="sxs-lookup"><span data-stu-id="5d326-182">In the call, the client:</span></span>

1. <span data-ttu-id="5d326-183">' I çağırarak yeni bir çift yönlü akış çağrısı başlatır `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="5d326-183">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="5d326-184">Hizmetini kullanarak hizmetten iletileri okumak için bir arka plan görevi oluşturur `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="5d326-184">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="5d326-185">Sunucusuna iletileri gönderir `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="5d326-185">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="5d326-186">Sunucuya ileti göndermeyi bitirmesinin tamamlandığını bildirir `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="5d326-186">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="5d326-187">Arka plan görevi tüm gelen iletileri okuuncaya kadar bekler.</span><span class="sxs-lookup"><span data-stu-id="5d326-187">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="5d326-188">Çift yönlü bir akış araması sırasında, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-188">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="5d326-189">Çift yönlü bir çağrı ile etkileşimde bulunmak için en iyi istemci mantığı, hizmet mantığına bağlı olarak farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="5d326-189">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="5d326-190">GRPC tanıtımlarına erişim</span><span class="sxs-lookup"><span data-stu-id="5d326-190">Access gRPC trailers</span></span>

<span data-ttu-id="5d326-191">gRPC çağrıları gRPC fragme döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-191">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="5d326-192">gRPC fragmanları, bir çağrı hakkında ad/değer meta verileri sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5d326-192">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="5d326-193">Treylik, HTTP üst bilgilerine benzer işlevler sağlar, ancak çağrının sonunda alınır.</span><span class="sxs-lookup"><span data-stu-id="5d326-193">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="5d326-194">gRPC tanıtımlarına kullanılarak erişilebilir `GetTrailers()` ve bu, meta veri koleksiyonu döndürür.</span><span class="sxs-lookup"><span data-stu-id="5d326-194">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="5d326-195">Yanıt tamamlandıktan sonra tanıtımları döndürülür, bu nedenle, tanıtıma erişmeden önce tüm yanıt iletilerini beklemelidir.</span><span class="sxs-lookup"><span data-stu-id="5d326-195">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="5d326-196">Tek birli ve istemci akış çağrıları `ResponseAsync` çağrılmadan önce await olmalıdır `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="5d326-196">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="5d326-197">Sunucu ve çift yönlü akış çağrılarının, çağrılmadan önce yanıt akışını beklemesi gerekir `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="5d326-197">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="5d326-198">gRPC tanıtımlarına da adresinden erişilebilir `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="5d326-198">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="5d326-199">Bir hizmet, tamam olmayan bir gRPC durumuyla birlikte Treyi döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-199">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="5d326-200">Bu durumda, daha sonra gRPC istemcisi tarafından oluşturulan özel durumdan tanıtımları alınır:</span><span class="sxs-lookup"><span data-stu-id="5d326-200">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="configure-deadline"></a><span data-ttu-id="5d326-201">Son tarihi Yapılandır</span><span class="sxs-lookup"><span data-stu-id="5d326-201">Configure deadline</span></span>

<span data-ttu-id="5d326-202">Bir çağrının ne kadar süreyle çalıştırılabildiğinden bir üst sınır sağladığından bir gRPC çağrı son tarihinin yapılandırılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="5d326-202">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="5d326-203">Yanlış çalışan hizmetlerin sürekli olarak çalıştırılmasını ve sunucu kaynaklarının tüketilmesi işlemini sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="5d326-203">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="5d326-204">Son tarihler, güvenilir uygulamalar oluşturmaya yönelik yararlı bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="5d326-204">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="5d326-205">`CallOptions.Deadline`Bir gRPC çağrısının son tarihini ayarlamak için yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="5d326-205">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="5d326-206">Daha fazla bilgi için bkz. <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="5d326-206">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d326-207">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5d326-207">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
