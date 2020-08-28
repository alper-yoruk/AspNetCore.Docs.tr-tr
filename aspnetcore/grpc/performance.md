---
title: GRPC ile en iyi performans uygulamaları
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
ms.openlocfilehash: 7d4d5732e6edb0d0a156fdcec5f59cc09a69d7de
ms.sourcegitcommit: 111b4e451da2e275fb074cde5d8a84b26a81937d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89040885"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="9efa1-103">GRPC ile en iyi performans uygulamaları</span><span class="sxs-lookup"><span data-stu-id="9efa1-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="9efa1-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9efa1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9efa1-105">gRPC, yüksek performanslı hizmetler için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="9efa1-106">Bu belgede, gRPC 'den mümkün olan en iyi performansı alma açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="9efa1-107">GRPC kanallarını yeniden kullanma</span><span class="sxs-lookup"><span data-stu-id="9efa1-107">Reuse gRPC channels</span></span>

<span data-ttu-id="9efa1-108">GRPC araması yapıldığında bir gRPC kanalının kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="9efa1-109">Bir kanalı yeniden kullanmak, çağrıların mevcut bir HTTP/2 bağlantısıyla çoğullanmış olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="9efa1-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="9efa1-110">Her gRPC çağrısı için yeni bir kanal oluşturulursa, tamamlanma süresi önemli ölçüde artabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="9efa1-111">Her çağrının, istemci ile sunucu arasında yeni bir HTTP/2 bağlantısı oluşturmak için birden çok ağ gidiş dönüşmesini gerekir:</span><span class="sxs-lookup"><span data-stu-id="9efa1-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="9efa1-112">Yuva açma</span><span class="sxs-lookup"><span data-stu-id="9efa1-112">Opening a socket</span></span>
2. <span data-ttu-id="9efa1-113">TCP bağlantısı kuruluyor</span><span class="sxs-lookup"><span data-stu-id="9efa1-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="9efa1-114">TLS anlaşması</span><span class="sxs-lookup"><span data-stu-id="9efa1-114">Negotiating TLS</span></span>
4. <span data-ttu-id="9efa1-115">HTTP/2 bağlantısı başlatılıyor</span><span class="sxs-lookup"><span data-stu-id="9efa1-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="9efa1-116">GRPC çağrısını yapma</span><span class="sxs-lookup"><span data-stu-id="9efa1-116">Making the gRPC call</span></span>

<span data-ttu-id="9efa1-117">Kanalların gRPC çağrıları arasında paylaşılması ve yeniden kullanılması güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="9efa1-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="9efa1-118">gRPC istemcileri kanallarla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9efa1-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="9efa1-119">gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9efa1-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="9efa1-120">Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="9efa1-121">Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="9efa1-122">Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="9efa1-123">Bağlantı eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="9efa1-123">Connection concurrency</span></span>

<span data-ttu-id="9efa1-124">HTTP/2 bağlantıları genellikle bir bağlantı üzerinde aynı anda [en fazla eşzamanlı akış (ETKIN http isteği)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) sayısı için bir sınıra sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="9efa1-125">Varsayılan olarak, çoğu sunucu bu sınırı 100 eşzamanlı akış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9efa1-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="9efa1-126">GRPC kanalı tek bir HTTP/2 bağlantısı kullanır ve eşzamanlı çağrılar bu bağlantı üzerinde çoğullanmış.</span><span class="sxs-lookup"><span data-stu-id="9efa1-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="9efa1-127">Etkin çağrıların sayısı bağlantı akışı sınırına ulaştığında, istemci içinde ek çağrılar sıraya alınır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="9efa1-128">Kuyruğa alınan çağrılar, gönderilmeden önce etkin çağrıların tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="9efa1-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="9efa1-129">Yüksek yük veya uzun süren akış gRPC çağrılarına sahip uygulamalar, bu sınır nedeniyle çağrı kuyruğu oluşturma nedeniyle oluşan performans sorunlarını görebilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9efa1-130">.NET 5, `SocketsHttpHandler.EnableMultipleHttp2Connections` özelliği tanıtır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="9efa1-131">Olarak ayarlandığında `true` , eş zamanlı akış sınırına ulaşıldığında ek http/2 bağlantıları bir kanal tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9efa1-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="9efa1-132">Bir `GrpcChannel` oluşturulduğunda, iç, `SocketsHttpHandler` ek http/2 bağlantıları oluşturacak şekilde otomatik olarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="9efa1-133">Bir uygulama kendi işleyicisini yapılandırıyorsa, aşağıdakileri yapmak için aşağıdakileri göz önünde bulundurun `EnableMultipleHttp2Connections` `true` :</span><span class="sxs-lookup"><span data-stu-id="9efa1-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="9efa1-134">.NET Core 3,1 uygulamaları için birkaç geçici çözüm vardır:</span><span class="sxs-lookup"><span data-stu-id="9efa1-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="9efa1-135">Yüksek yük ile uygulamanın bölgeleri için ayrı gRPC kanalları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9efa1-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="9efa1-136">Örneğin, `Logger` GRPC hizmeti yüksek bir yüke sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="9efa1-137">Uygulamada oluşturmak için ayrı bir kanal kullanın `LoggerClient` .</span><span class="sxs-lookup"><span data-stu-id="9efa1-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="9efa1-138">GRPC kanalları havuzunu kullanın, örneğin, gRPC kanallarının bir listesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9efa1-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="9efa1-139">`Random` Her bir gRPC kanalı gerektiğinde listeden bir kanal seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="9efa1-140">`Random`Birden çok bağlantı üzerinde rastgele bir şekilde çağrı dağıtır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9efa1-141">Sunucuda maksimum eşzamanlı akış sınırının artırılması, bu sorunu çözmenin başka bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="9efa1-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="9efa1-142">Kestrel içinde, ile yapılandırılır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="9efa1-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="9efa1-143">En fazla eşzamanlı akış sınırının artırılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="9efa1-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="9efa1-144">Tek bir HTTP/2 bağlantısında çok fazla akış yeni performans sorunları sunuyor:</span><span class="sxs-lookup"><span data-stu-id="9efa1-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="9efa1-145">Bağlantıya yazmaya çalışan akışlar arasında iş parçacığı çakışması.</span><span class="sxs-lookup"><span data-stu-id="9efa1-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="9efa1-146">Bağlantı paketi kaybı, TCP katmanında tüm çağrıların engellenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9efa1-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="9efa1-147">Yük dengeleme</span><span class="sxs-lookup"><span data-stu-id="9efa1-147">Load balancing</span></span>

<span data-ttu-id="9efa1-148">Bazı yük dengeleyiciler gRPC ile etkili bir şekilde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="9efa1-148">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="9efa1-149">L4 (taşıma) yük dengeleyiciler, TCP bağlantılarını uç noktalar arasında dağıtarak bağlantı düzeyinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-149">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="9efa1-150">Bu yaklaşım, HTTP/1.1 ile yapılan Yük Dengeleme API çağrılarının yüklenmesi için iyi bir sonuç verir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-150">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="9efa1-151">HTTP/1.1 ile yapılan eşzamanlı çağrılar farklı bağlantılarda gönderilir ve çağrılar, uç noktalar arasında yük dengelemesi yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-151">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="9efa1-152">L4 yük dengeleyiciler bir bağlantı düzeyinde çalıştığından, gRPC ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="9efa1-152">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="9efa1-153">gRPC, tek bir TCP bağlantısında birden çok çağrının çoğullanmış HTTP/2 kullanır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-153">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="9efa1-154">Bu bağlantı üzerindeki tüm gRPC çağrıları bir uç noktaya gider.</span><span class="sxs-lookup"><span data-stu-id="9efa1-154">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="9efa1-155">GRPC 'nin yükünü etkin şekilde dengelemek için iki seçenek vardır:</span><span class="sxs-lookup"><span data-stu-id="9efa1-155">There are two options to effectively load balance gRPC:</span></span>

1. <span data-ttu-id="9efa1-156">İstemci tarafı Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="9efa1-156">Client-side load balancing</span></span>
2. <span data-ttu-id="9efa1-157">L7 (uygulama) proxy Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="9efa1-157">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="9efa1-158">Yalnızca gRPC çağrıları uç noktalar arasında yük dengelenebilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-158">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="9efa1-159">Akış gRPC çağrısı kurulduktan sonra, akış üzerinden gönderilen tüm iletiler bir uç noktaya gider.</span><span class="sxs-lookup"><span data-stu-id="9efa1-159">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="9efa1-160">İstemci tarafı Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="9efa1-160">Client-side load balancing</span></span>

<span data-ttu-id="9efa1-161">İstemci tarafı yük dengelemesi ile istemci, uç noktalar hakkında bilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-161">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="9efa1-162">Her gRPC çağrısı için, çağrısını göndermek üzere farklı bir uç nokta seçer.</span><span class="sxs-lookup"><span data-stu-id="9efa1-162">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="9efa1-163">Gecikme süresi önemli olduğunda istemci tarafı yük dengelemesi iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-163">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="9efa1-164">İstemci ile hizmet arasında, çağrının hizmete doğrudan gönderilmesi için bir ara sunucu yok.</span><span class="sxs-lookup"><span data-stu-id="9efa1-164">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="9efa1-165">İstemci tarafı yük dengelemenin dezavantajı, her istemcinin kullanması gereken kullanılabilir uç noktaları izlemesine sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-165">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="9efa1-166">Arabelleği istemci yük dengelemesi, Yük Dengeleme durumunun merkezi bir konumda depolandığı bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-166">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="9efa1-167">İstemciler, Yük Dengeleme kararları verirken kullanılacak bilgiler için merkezi konumu düzenli aralıklarla sorgular.</span><span class="sxs-lookup"><span data-stu-id="9efa1-167">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="9efa1-168">`Grpc.Net.Client` Şu anda istemci tarafı yük dengelemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9efa1-168">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="9efa1-169">.NET ' te istemci tarafı yük dengelemesi gerekliyse, [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-169">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="9efa1-170">Proxy yük dengelemesi</span><span class="sxs-lookup"><span data-stu-id="9efa1-170">Proxy load balancing</span></span>

<span data-ttu-id="9efa1-171">Bir L7 (uygulama) proxy 'si, bir L4 (aktarım) proxy 'si tarafından daha yüksek bir düzeyde çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-171">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="9efa1-172">L7 proxy 'leri HTTP/2 ' yi anlayın ve birden fazla uç nokta genelinde tek bir HTTP/2 bağlantısı üzerinde gRPC çağrılarını bir tane sunucuya dağıtabiliyor.</span><span class="sxs-lookup"><span data-stu-id="9efa1-172">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="9efa1-173">Proxy kullanmak, istemci tarafı yük dengelemeden daha basittir, ancak gRPC çağrılarına ek gecikme süresi ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-173">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="9efa1-174">Kullanılabilir çok sayıda L7 proxy vardır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-174">There are many L7 proxies available.</span></span> <span data-ttu-id="9efa1-175">Bazı seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9efa1-175">Some options are:</span></span>

1. <span data-ttu-id="9efa1-176">[Envoy](https://www.envoyproxy.io/) proxy-popüler bir açık kaynak proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9efa1-176">[Envoy](https://www.envoyproxy.io/) proxy - A popular open source proxy.</span></span>
2. <span data-ttu-id="9efa1-177">Kubernetes için [Linkerd](https://linkerd.io/) -hizmet ağı.</span><span class="sxs-lookup"><span data-stu-id="9efa1-177">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
2. <span data-ttu-id="9efa1-178">[Yarp: bir ters ara sunucu](https://microsoft.github.io/reverse-proxy/) -.net ' te yazılmış bir önizleme açık kaynak proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9efa1-178">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="9efa1-179">İşlem arası iletişim</span><span class="sxs-lookup"><span data-stu-id="9efa1-179">Inter-process communication</span></span>

<span data-ttu-id="9efa1-180">istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-180">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="9efa1-181">TCP bir ağ üzerinden iletişim kurmak için idealdir, ancak istemci ve hizmet aynı makinede olduğunda, [işlemler arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-181">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="9efa1-182">Aynı makinede bulunan süreçler arasında gRPC çağrıları için UNIX etki alanı yuvaları veya adlandırılmış kanallar gibi bir taşıma kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="9efa1-182">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="9efa1-183">Daha fazla bilgi için bkz. <xref:grpc/interprocess>.</span><span class="sxs-lookup"><span data-stu-id="9efa1-183">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="9efa1-184">Canlı ping pingleri tut</span><span class="sxs-lookup"><span data-stu-id="9efa1-184">Keep alive pings</span></span>

<span data-ttu-id="9efa1-185">Canlı tut ping işlemleri, etkin olmama süreleri boyunca HTTP/2 bağlantılarının etkin kalmasını sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-185">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="9efa1-186">Mevcut bir HTTP/2 bağlantısının, bir uygulamanın etkinliği sürdürülürse, yeniden yüklenmekte olan bağlantının neden olduğu bir gecikme süresi olmadan, ilk gRPC çağrılarının hızla yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-186">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="9efa1-187">Etkin tutma pingleri şu şekilde yapılandırılır <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="9efa1-187">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="9efa1-188">Yukarıdaki kod, işlem yapılmayan dönemler sırasında her 60 saniyede sunucuya canlı tutmayı canlı tut iletisi gönderen bir kanalı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-188">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="9efa1-189">Ping işlemi, sunucunun ve kullanımda olan tüm proxy 'lerin, etkin olmama nedeniyle bağlantıyı kapatmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="9efa1-189">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="9efa1-190">Akış</span><span class="sxs-lookup"><span data-stu-id="9efa1-190">Streaming</span></span>

<span data-ttu-id="9efa1-191">gRPC çift yönlü akışı, yüksek performanslı senaryolarda birli gRPC çağrılarını değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-191">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="9efa1-192">Çift yönlü bir akış başlatıldıktan sonra, akış iletileri geri ve ileri, birden çok birli gRPC çağrısı içeren iletiler göndermekten daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-192">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="9efa1-193">Akışlı iletiler, var olan bir HTTP/2 isteğine veri olarak gönderilir ve her birli çağrı için yeni bir HTTP/2 isteği oluşturma yükünü ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-193">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="9efa1-194">Örnek hizmet:</span><span class="sxs-lookup"><span data-stu-id="9efa1-194">Example service:</span></span>

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

<span data-ttu-id="9efa1-195">Örnek istemci:</span><span class="sxs-lookup"><span data-stu-id="9efa1-195">Example client:</span></span>

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

<span data-ttu-id="9efa1-196">Çift yönlü akışla birli çağrıları performans nedenleriyle değiştirmek, gelişmiş bir tekniktir ve birçok durumda uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-196">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="9efa1-197">Akış çağrılarının kullanılması şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="9efa1-197">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="9efa1-198">Yüksek aktarım hızı veya düşük gecikme süresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-198">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="9efa1-199">gRPC ve HTTP/2 performans sorunu olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-199">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="9efa1-200">İstemcideki bir çalışan, gRPC hizmeti ile normal iletiler gönderiyor veya alıyor.</span><span class="sxs-lookup"><span data-stu-id="9efa1-200">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="9efa1-201">Birli yerine akış çağrılarını kullanmanın ek karmaşıklığı ve sınırlamalarından haberdar olun:</span><span class="sxs-lookup"><span data-stu-id="9efa1-201">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="9efa1-202">Bir akış, bir hizmet veya bağlantı hatası tarafından kesintiye uğrar.</span><span class="sxs-lookup"><span data-stu-id="9efa1-202">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="9efa1-203">Bir hata varsa akışın yeniden başlatılması için mantık gereklidir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-203">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="9efa1-204">`RequestStream.WriteAsync` Çoklu iş parçacığı oluşturma için güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-204">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="9efa1-205">Tek seferde bir akışa yalnızca bir ileti yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-205">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="9efa1-206">Tek bir akış üzerinden birden çok iş parçacığından ileti göndermek için, Marshall iletileri gibi bir üretici/tüketici kuyruğu gerekir <xref:System.Threading.Channels.Channel%601> .</span><span class="sxs-lookup"><span data-stu-id="9efa1-206">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="9efa1-207">Bir gRPC akış yöntemi bir ileti türü alacak ve bir ileti türü göndermesiyle sınırlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="9efa1-207">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="9efa1-208">Örneğin, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` alır `RequestMessage` ve gönderir `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="9efa1-208">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="9efa1-209">Ve kullanılarak bilinmeyen veya koşullu iletiler için protoarabellek desteği `Any` `oneof` Bu sınırlamaya geçici çözüm verebilir.</span><span class="sxs-lookup"><span data-stu-id="9efa1-209">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
