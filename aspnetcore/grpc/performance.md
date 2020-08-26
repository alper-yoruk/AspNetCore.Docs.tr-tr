---
title: Performans için en iyi yöntemler
author: jamesnk
description: Yüksek performanslı gRPC hizmetleri oluşturmak için en iyi uygulamaları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866191"
---
# <a name="performance-best-practices"></a><span data-ttu-id="44600-103">Performans için en iyi yöntemler</span><span class="sxs-lookup"><span data-stu-id="44600-103">Performance best practices</span></span>

<span data-ttu-id="44600-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="44600-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="44600-105">gRPC, yüksek performanslı hizmetler için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="44600-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="44600-106">Bu belgede, gRPC 'den mümkün olan en iyi performansı alma açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="44600-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="44600-107">Kanalı yeniden kullan</span><span class="sxs-lookup"><span data-stu-id="44600-107">Reuse channel</span></span>

<span data-ttu-id="44600-108">GRPC araması yapıldığında bir gRPC kanalının kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="44600-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="44600-109">Bir kanalı yeniden kullanmak, çağrıların mevcut bir HTTP/2 bağlantısıyla çoğullanmış olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="44600-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="44600-110">Her gRPC çağrısı için yeni bir kanal oluşturulursa, tamamlanma süresi önemli ölçüde artabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="44600-111">Her çağrının, istemci ile sunucu arasında bir HTTP/2 bağlantısı oluşturmak için birden çok ağ gidiş dönüşmesini gerekir:</span><span class="sxs-lookup"><span data-stu-id="44600-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="44600-112">Yuva açma</span><span class="sxs-lookup"><span data-stu-id="44600-112">Opening a socket</span></span>
2. <span data-ttu-id="44600-113">TCP bağlantısı kuruluyor</span><span class="sxs-lookup"><span data-stu-id="44600-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="44600-114">TLS anlaşması</span><span class="sxs-lookup"><span data-stu-id="44600-114">Negotiating TLS</span></span>
4. <span data-ttu-id="44600-115">HTTP/2 bağlantısı başlatılıyor</span><span class="sxs-lookup"><span data-stu-id="44600-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="44600-116">GRPC çağrısını yapma</span><span class="sxs-lookup"><span data-stu-id="44600-116">Making the gRPC call</span></span>

<span data-ttu-id="44600-117">Kanalların gRPC çağrıları arasında paylaşılması ve yeniden kullanılması güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="44600-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="44600-118">gRPC istemcileri kanallarla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="44600-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="44600-119">gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="44600-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="44600-120">Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="44600-121">Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="44600-122">Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="44600-123">Bağlantı eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="44600-123">Connection concurrency</span></span>

<span data-ttu-id="44600-124">HTTP/2 bağlantıları genellikle bir bağlantı üzerinde aynı anda [en fazla eşzamanlı akış (ETKIN http isteği)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) sayısı için bir sınıra sahiptir.</span><span class="sxs-lookup"><span data-stu-id="44600-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="44600-125">Varsayılan olarak, çoğu sunucu bu sınırı 100 eşzamanlı akış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="44600-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="44600-126">GRPC kanalı tek bir HTTP/2 bağlantısı kullanır ve eşzamanlı çağrılar bu bağlantı üzerinde çoğullanmış.</span><span class="sxs-lookup"><span data-stu-id="44600-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="44600-127">Etkin çağrıların sayısı bağlantı akışı sınırına ulaştığında, istemci içinde ek çağrılar sıraya alınır.</span><span class="sxs-lookup"><span data-stu-id="44600-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="44600-128">Kuyruğa alınan çağrılar, gönderilmeden önce etkin çağrıların tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="44600-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="44600-129">Yüksek yük veya uzun süren akış gRPC çağrılarına sahip uygulamalar, bu sınır nedeniyle çağrı kuyruğu oluşturma nedeniyle oluşan performans sorunlarını görebilir.</span><span class="sxs-lookup"><span data-stu-id="44600-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="44600-130">.NET 5, `SocketsHttpHandler.EnableMultipleHttp2Connections` özelliği tanıtır.</span><span class="sxs-lookup"><span data-stu-id="44600-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="44600-131">Olarak ayarlandığında `true` , eş zamanlı akış sınırına ulaşıldığında ek http/2 bağlantıları bir kanal tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="44600-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="44600-132">Bir `GrpcChannel` oluşturulduğunda, iç, `SocketsHttpHandler` ek http/2 bağlantıları oluşturacak şekilde otomatik olarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="44600-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="44600-133">Bir uygulama kendi işleyicisini yapılandırıyorsa, aşağıdakileri yapmak için aşağıdakileri göz önünde bulundurun `EnableMultipleHttp2Connections` `true` :</span><span class="sxs-lookup"><span data-stu-id="44600-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="44600-134">.NET Core 3,1 uygulamaları için birkaç geçici çözüm vardır:</span><span class="sxs-lookup"><span data-stu-id="44600-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="44600-135">Yüksek yük ile uygulamanın bölgeleri için ayrı gRPC kanalları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="44600-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="44600-136">Örneğin, `Logger` GRPC hizmeti yüksek bir yüke sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="44600-137">Uygulamada oluşturmak için ayrı bir kanal kullanın `LoggerClient` .</span><span class="sxs-lookup"><span data-stu-id="44600-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="44600-138">GRPC kanalları havuzunu kullanın, örneğin, gRPC kanallarının bir listesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="44600-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="44600-139">`Random` Her bir gRPC kanalı gerektiğinde listeden bir kanal seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="44600-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="44600-140">`Random`Birden çok bağlantı üzerinde rastgele bir şekilde çağrı dağıtır.</span><span class="sxs-lookup"><span data-stu-id="44600-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44600-141">Sunucuda maksimum eşzamanlı akış sınırının artırılması, bu sorunu çözmenin başka bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="44600-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="44600-142">Kestrel içinde, ile yapılandırılır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="44600-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="44600-143">En fazla eşzamanlı akış sınırının artırılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="44600-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="44600-144">Tek bir HTTP/2 bağlantısında çok fazla akış yeni performans sorunları sunuyor:</span><span class="sxs-lookup"><span data-stu-id="44600-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="44600-145">Bağlantıya yazmaya çalışan akışlar arasında iş parçacığı çakışması.</span><span class="sxs-lookup"><span data-stu-id="44600-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="44600-146">Bağlantı paketi kaybı, TCP katmanında tüm çağrıların engellenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="44600-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="44600-147">Canlı ping pingleri tut</span><span class="sxs-lookup"><span data-stu-id="44600-147">Keep alive pings</span></span>

<span data-ttu-id="44600-148">Canlı tut ping işlemleri, etkin olmama süreleri boyunca HTTP/2 bağlantılarının etkin kalmasını sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="44600-149">Mevcut bir HTTP/2 bağlantısının, bir uygulamanın etkinliği sürdürülürse, yeniden yüklenmekte olan bağlantının neden olduğu bir gecikme süresi olmadan, ilk gRPC çağrılarının hızla yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="44600-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="44600-150">Etkin tutma pingleri şu şekilde yapılandırılır <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="44600-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="44600-151">Yukarıdaki kod, işlem yapılmayan dönemler sırasında her 60 saniyede sunucuya canlı tutmayı canlı tut iletisi gönderen bir kanalı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="44600-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="44600-152">Ping işlemi, sunucunun ve kullanımda olan tüm proxy 'lerin, etkin olmama nedeniyle bağlantıyı kapatmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="44600-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="44600-153">Akış</span><span class="sxs-lookup"><span data-stu-id="44600-153">Streaming</span></span>

<span data-ttu-id="44600-154">gRPC çift yönlü akışı, yüksek performanslı senaryolarda birli gRPC çağrılarını değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="44600-155">Çift yönlü bir akış başlatıldıktan sonra, akış iletileri geri ve ileri, birden çok birli gRPC çağrısı içeren iletiler göndermekten daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="44600-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="44600-156">Akışlı iletiler, var olan bir HTTP/2 isteğine veri olarak gönderilir ve her birli çağrı için yeni bir HTTP/2 isteği oluşturma yükünü ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="44600-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="44600-157">Örnek hizmet:</span><span class="sxs-lookup"><span data-stu-id="44600-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="44600-158">Örnek istemci:</span><span class="sxs-lookup"><span data-stu-id="44600-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="44600-159">Çift yönlü akışla birli çağrıları performans nedenleriyle değiştirmek, gelişmiş bir tekniktir ve birçok durumda uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="44600-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="44600-160">Akış çağrılarının kullanılması şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="44600-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="44600-161">Yüksek aktarım hızı veya düşük gecikme süresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="44600-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="44600-162">gRPC ve HTTP/2 performans sorunu olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="44600-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="44600-163">İstemcideki bir çalışan, gRPC hizmeti ile normal iletiler gönderiyor veya alıyor.</span><span class="sxs-lookup"><span data-stu-id="44600-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="44600-164">Birli yerine akış çağrılarını kullanmanın ek karmaşıklığı ve sınırlamalarından haberdar olun:</span><span class="sxs-lookup"><span data-stu-id="44600-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="44600-165">Bir akış, bir hizmet veya bağlantı hatası tarafından kesintiye uğrar.</span><span class="sxs-lookup"><span data-stu-id="44600-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="44600-166">Bir hata varsa akışın yeniden başlatılması için mantık gereklidir.</span><span class="sxs-lookup"><span data-stu-id="44600-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="44600-167">`RequestStream.WriteAsync` Çoklu iş parçacığı oluşturma için güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="44600-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="44600-168">Tek seferde bir akışa yalnızca bir ileti yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="44600-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="44600-169">Tek bir akış üzerinden birden çok iş parçacığından ileti göndermek için, Marshall iletileri gibi bir üretici/tüketici kuyruğu gerekir <xref:System.Threading.Channels.Channel%601> .</span><span class="sxs-lookup"><span data-stu-id="44600-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="44600-170">Bir gRPC akış yöntemi bir ileti türü alacak ve bir ileti türü göndermesiyle sınırlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="44600-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="44600-171">Örneğin, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` alır `RequestMessage` ve gönderir `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="44600-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="44600-172">Ve kullanılarak bilinmeyen veya koşullu iletiler için protoarabellek desteği `Any` `oneof` Bu sınırlamaya geçici çözüm verebilir.</span><span class="sxs-lookup"><span data-stu-id="44600-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
