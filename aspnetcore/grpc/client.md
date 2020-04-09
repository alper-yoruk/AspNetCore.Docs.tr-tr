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
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="862d2-103">.NET istemcisi ile gRPC hizmetlerini arayın</span><span class="sxs-lookup"><span data-stu-id="862d2-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="862d2-104">Bir .NET gRPC istemci kitaplığı [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet paketinde mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="862d2-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="862d2-105">Bu belge, nasıl açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="862d2-105">This document explains how to:</span></span>

* <span data-ttu-id="862d2-106">gRPC hizmetlerini aramak için bir gRPC istemcisini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="862d2-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="862d2-107">Unary, sunucu akışı, istemci akışı ve çift yönlü akış yöntemleri için gRPC aramaları yapın.</span><span class="sxs-lookup"><span data-stu-id="862d2-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="862d2-108">gRPC istemcisi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="862d2-108">Configure gRPC client</span></span>

<span data-ttu-id="862d2-109">gRPC istemcileri [ \* \*.proto\* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir.</span><span class="sxs-lookup"><span data-stu-id="862d2-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="862d2-110">Somut gRPC istemcisi \* \*.proto\* dosyasındaki gRPC hizmetine çevrilen yöntemlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="862d2-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="862d2-111">Bir gRPC istemcisi bir kanaldan oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="862d2-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="862d2-112">Bir kanal `GrpcChannel.ForAddress` oluşturmak için kullanarak başlayın ve sonra bir gRPC istemcisi oluşturmak için kanalı kullanın:</span><span class="sxs-lookup"><span data-stu-id="862d2-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="862d2-113">Kanal, gRPC hizmetiyle uzun süreli bir bağlantıyı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="862d2-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="862d2-114">Bir kanal oluşturulduğunda, bir hizmeti çağırmayla ilgili seçeneklerle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="862d2-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="862d2-115">Örneğin, arama `HttpClient` yapmak için kullanılan, en fazla gönder ve ileti boyutu `GrpcChannelOptions` ve `GrpcChannel.ForAddress`günlük belirtilebilir ve .</span><span class="sxs-lookup"><span data-stu-id="862d2-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="862d2-116">Seçeneklerin tam listesi için [istemci yapılandırma seçeneklerine](xref:grpc/configuration#configure-client-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="862d2-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="862d2-117">Kanal ve istemci performansı ve kullanımı:</span><span class="sxs-lookup"><span data-stu-id="862d2-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="862d2-118">Kanal oluşturmak pahalı bir işlem olabilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="862d2-119">gRPC aramaları için bir kanalı yeniden kullanmak performans avantajları sağlar.</span><span class="sxs-lookup"><span data-stu-id="862d2-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="862d2-120">gRPC istemcileri kanallarla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="862d2-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="862d2-121">gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="862d2-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="862d2-122">Farklı istemci türleri de dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="862d2-123">Kanaldan oluşturulan bir kanal ve istemciler birden çok iş parçacığı tarafından güvenle kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="862d2-124">Kanaldan oluşturulan istemciler birden çok eşzamanlı arama yapabilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="862d2-125">`GrpcChannel.ForAddress`gRPC istemcisi oluşturmak için tek seçenek değildir.</span><span class="sxs-lookup"><span data-stu-id="862d2-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="862d2-126">bir ASP.NET Core uygulamasından gRPC hizmetlerini arıyorsanız, [gRPC istemci fabrika tümleştirmesi](xref:grpc/clientfactory)göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="862d2-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="862d2-127">gRPC istemcileri oluşturmak için merkezi bir alternatif sunar. `HttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="862d2-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="862d2-128">[.NET istemcisi ile güvenli olmayan gRPC hizmetlerini aramak](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)için ek yapılandırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="862d2-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="862d2-129">HTTP/2 üzerinden `Grpc.Net.Client` gRPC arama şu anda Xamarin desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="862d2-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="862d2-130">Biz gelecekteki bir Xamarin sürümünde HTTP / 2 desteği geliştirmek için çalışıyoruz.</span><span class="sxs-lookup"><span data-stu-id="862d2-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="862d2-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) ve [gRPC-Web](xref:grpc/browser) bugün çalışmak uygun alternatifler vardır.</span><span class="sxs-lookup"><span data-stu-id="862d2-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="862d2-132">gRPC aramaları yapma</span><span class="sxs-lookup"><span data-stu-id="862d2-132">Make gRPC calls</span></span>

<span data-ttu-id="862d2-133">Bir gRPC arama istemciüzerinde bir yöntem çağırarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="862d2-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="862d2-134">gRPC istemcisi ileti serileştirme ve doğru hizmet için gRPC arama adresleme işleyecek.</span><span class="sxs-lookup"><span data-stu-id="862d2-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="862d2-135">gRPC'nin farklı yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="862d2-135">gRPC has different types of methods.</span></span> <span data-ttu-id="862d2-136">GRPC araması yapmak için istemciyi nasıl kullanacağınız aradığınız yöntemin türüne bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="862d2-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="862d2-137">gRPC yöntem türleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="862d2-137">The gRPC method types are:</span></span>

* <span data-ttu-id="862d2-138">Tekli</span><span class="sxs-lookup"><span data-stu-id="862d2-138">Unary</span></span>
* <span data-ttu-id="862d2-139">Sunucu akışı</span><span class="sxs-lookup"><span data-stu-id="862d2-139">Server streaming</span></span>
* <span data-ttu-id="862d2-140">İstemci akışı</span><span class="sxs-lookup"><span data-stu-id="862d2-140">Client streaming</span></span>
* <span data-ttu-id="862d2-141">Çift yönlü akış</span><span class="sxs-lookup"><span data-stu-id="862d2-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="862d2-142">Unary çağrı</span><span class="sxs-lookup"><span data-stu-id="862d2-142">Unary call</span></span>

<span data-ttu-id="862d2-143">Unary arama istemcibir istek iletisi gönderme ile başlar.</span><span class="sxs-lookup"><span data-stu-id="862d2-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="862d2-144">Hizmet bittiğinde bir yanıt iletisi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="862d2-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="862d2-145">\* \*.proto\* dosyasındaki her bir unary servis yöntemi, metodu çağırmak için beton gRPC istemci türünde iki .NET yöntemiyle sonuçlanır: bir senkron yöntem ve engelleme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="862d2-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="862d2-146">Örneğin, arama `GreeterClient` nın iki yolu `SayHello`vardır:</span><span class="sxs-lookup"><span data-stu-id="862d2-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="862d2-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` hizmeti eş zamanlı olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="862d2-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="862d2-148">Beklenebilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-148">Can be awaited.</span></span>
* <span data-ttu-id="862d2-149">`GreeterClient.SayHello`- `Greeter.SayHello` tamamlanana kadar servis ve bloklar çağırır.</span><span class="sxs-lookup"><span data-stu-id="862d2-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="862d2-150">Eşzamanlı kod kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="862d2-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="862d2-151">Sunucu akış çağrısı</span><span class="sxs-lookup"><span data-stu-id="862d2-151">Server streaming call</span></span>

<span data-ttu-id="862d2-152">Sunucu akışı çağrısı istemcinin istek iletisi göndermesiyle başlar.</span><span class="sxs-lookup"><span data-stu-id="862d2-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="862d2-153">`ResponseStream.MoveNext()`hizmetten aktarılan iletileri okur.</span><span class="sxs-lookup"><span data-stu-id="862d2-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="862d2-154">Sunucu akışı çağrısı döndürdüğünde `ResponseStream.MoveNext()` `false`tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="862d2-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="862d2-155">C# 8 veya daha sonra `await foreach` kullanıyorsanız, sözdizimi iletileri okumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="862d2-156">Uzantı `IAsyncStreamReader<T>.ReadAllAsync()` yöntemi yanıt akışındaki tüm iletileri okur:</span><span class="sxs-lookup"><span data-stu-id="862d2-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="862d2-157">İstemci akışı çağrısı</span><span class="sxs-lookup"><span data-stu-id="862d2-157">Client streaming call</span></span>

<span data-ttu-id="862d2-158">İstemci akışı çağrısı, istemci ileti *göndermeden* başlar.</span><span class="sxs-lookup"><span data-stu-id="862d2-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="862d2-159">İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="862d2-160">İstemci ileti göndermeyi `RequestStream.CompleteAsync` bitirdiğinde hizmeti bildirmek için çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="862d2-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="862d2-161">Hizmet bir yanıt iletisi döndürdüğünde çağrı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="862d2-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="862d2-162">Çift yönlü akış çağrısı</span><span class="sxs-lookup"><span data-stu-id="862d2-162">Bi-directional streaming call</span></span>

<span data-ttu-id="862d2-163">İstemci ileti *göndermeden* çift yönlü akış çağrısı başlar.</span><span class="sxs-lookup"><span data-stu-id="862d2-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="862d2-164">İstemci `RequestStream.WriteAsync`ile ileti göndermeyi seçebilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="862d2-165">Hizmetten aktarılan iletilere veya `ResponseStream.MoveNext()` . `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="862d2-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="862d2-166">Daha fazla ileti olmadığında `ResponseStream` çift yönlü akış çağrısı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="862d2-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="862d2-167">Çift yönlü akış araması sırasında, istemci ve hizmet istediği zaman birbirlerine ileti gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="862d2-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="862d2-168">Çift yönlü bir çağrıyla etkileşim kurmak için en iyi istemci mantığı hizmet mantığına bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="862d2-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="862d2-169">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="862d2-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
