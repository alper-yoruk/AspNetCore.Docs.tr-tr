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
ms.openlocfilehash: a0a1a6901e07fb0074ca403870378f267d3d4403
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379451"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="8106a-103">GRPC ile en iyi performans uygulamaları</span><span class="sxs-lookup"><span data-stu-id="8106a-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="8106a-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8106a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="8106a-105">gRPC, yüksek performanslı hizmetler için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="8106a-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="8106a-106">Bu belgede, gRPC 'den mümkün olan en iyi performansı alma açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8106a-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="8106a-107">GRPC kanallarını yeniden kullanma</span><span class="sxs-lookup"><span data-stu-id="8106a-107">Reuse gRPC channels</span></span>

<span data-ttu-id="8106a-108">GRPC araması yapıldığında bir gRPC kanalının kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8106a-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="8106a-109">Bir kanalı yeniden kullanmak, çağrıların mevcut bir HTTP/2 bağlantısıyla çoğullanmış olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="8106a-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="8106a-110">Her gRPC çağrısı için yeni bir kanal oluşturulursa, tamamlanma süresi önemli ölçüde artabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="8106a-111">Her çağrının, istemci ile sunucu arasında yeni bir HTTP/2 bağlantısı oluşturmak için birden çok ağ gidiş dönüşmesini gerekir:</span><span class="sxs-lookup"><span data-stu-id="8106a-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="8106a-112">Yuva açma</span><span class="sxs-lookup"><span data-stu-id="8106a-112">Opening a socket</span></span>
2. <span data-ttu-id="8106a-113">TCP bağlantısı kuruluyor</span><span class="sxs-lookup"><span data-stu-id="8106a-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="8106a-114">TLS anlaşması</span><span class="sxs-lookup"><span data-stu-id="8106a-114">Negotiating TLS</span></span>
4. <span data-ttu-id="8106a-115">HTTP/2 bağlantısı başlatılıyor</span><span class="sxs-lookup"><span data-stu-id="8106a-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="8106a-116">GRPC çağrısını yapma</span><span class="sxs-lookup"><span data-stu-id="8106a-116">Making the gRPC call</span></span>

<span data-ttu-id="8106a-117">Kanalların gRPC çağrıları arasında paylaşılması ve yeniden kullanılması güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="8106a-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="8106a-118">gRPC istemcileri kanallarla oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8106a-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="8106a-119">gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="8106a-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="8106a-120">Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="8106a-121">Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="8106a-122">Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="8106a-123">gRPC istemci fabrikası, kanalları yapılandırmak için merkezi bir yöntem sunar.</span><span class="sxs-lookup"><span data-stu-id="8106a-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="8106a-124">Temel kanalları otomatik olarak yeniden kullanır.</span><span class="sxs-lookup"><span data-stu-id="8106a-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="8106a-125">Daha fazla bilgi için bkz. <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="8106a-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="8106a-126">Bağlantı eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="8106a-126">Connection concurrency</span></span>

<span data-ttu-id="8106a-127">HTTP/2 bağlantıları genellikle bir bağlantı üzerinde aynı anda [en fazla eşzamanlı akış (ETKIN http isteği)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) sayısı için bir sınıra sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8106a-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="8106a-128">Varsayılan olarak, çoğu sunucu bu sınırı 100 eşzamanlı akış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="8106a-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="8106a-129">GRPC kanalı tek bir HTTP/2 bağlantısı kullanır ve eşzamanlı çağrılar bu bağlantı üzerinde çoğullanmış.</span><span class="sxs-lookup"><span data-stu-id="8106a-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="8106a-130">Etkin çağrıların sayısı bağlantı akışı sınırına ulaştığında, istemci içinde ek çağrılar sıraya alınır.</span><span class="sxs-lookup"><span data-stu-id="8106a-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="8106a-131">Kuyruğa alınan çağrılar, gönderilmeden önce etkin çağrıların tamamlanmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="8106a-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="8106a-132">Yüksek yük veya uzun süren akış gRPC çağrılarına sahip uygulamalar, bu sınır nedeniyle çağrı kuyruğu oluşturma nedeniyle oluşan performans sorunlarını görebilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8106a-133">.NET 5, `SocketsHttpHandler.EnableMultipleHttp2Connections` özelliği tanıtır.</span><span class="sxs-lookup"><span data-stu-id="8106a-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="8106a-134">Olarak ayarlandığında `true` , eş zamanlı akış sınırına ulaşıldığında ek http/2 bağlantıları bir kanal tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8106a-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="8106a-135">Bir `GrpcChannel` oluşturulduğunda, iç, `SocketsHttpHandler` ek http/2 bağlantıları oluşturacak şekilde otomatik olarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="8106a-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="8106a-136">Bir uygulama kendi işleyicisini yapılandırıyorsa, aşağıdakileri yapmak için aşağıdakileri göz önünde bulundurun `EnableMultipleHttp2Connections` `true` :</span><span class="sxs-lookup"><span data-stu-id="8106a-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="8106a-137">.NET Core 3,1 uygulamaları için birkaç geçici çözüm vardır:</span><span class="sxs-lookup"><span data-stu-id="8106a-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="8106a-138">Yüksek yük ile uygulamanın bölgeleri için ayrı gRPC kanalları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8106a-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="8106a-139">Örneğin, `Logger` GRPC hizmeti yüksek bir yüke sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="8106a-140">Uygulamada oluşturmak için ayrı bir kanal kullanın `LoggerClient` .</span><span class="sxs-lookup"><span data-stu-id="8106a-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="8106a-141">GRPC kanalları havuzunu kullanın, örneğin, gRPC kanallarının bir listesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8106a-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="8106a-142">`Random` Her bir gRPC kanalı gerektiğinde listeden bir kanal seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8106a-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="8106a-143">`Random`Birden çok bağlantı üzerinde rastgele bir şekilde çağrı dağıtır.</span><span class="sxs-lookup"><span data-stu-id="8106a-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8106a-144">Sunucuda maksimum eşzamanlı akış sınırının artırılması, bu sorunu çözmenin başka bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="8106a-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="8106a-145">Kestrel içinde, ile yapılandırılır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="8106a-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="8106a-146">En fazla eşzamanlı akış sınırının artırılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="8106a-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="8106a-147">Tek bir HTTP/2 bağlantısında çok fazla akış yeni performans sorunları sunuyor:</span><span class="sxs-lookup"><span data-stu-id="8106a-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="8106a-148">Bağlantıya yazmaya çalışan akışlar arasında iş parçacığı çakışması.</span><span class="sxs-lookup"><span data-stu-id="8106a-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="8106a-149">Bağlantı paketi kaybı, TCP katmanında tüm çağrıların engellenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="8106a-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="8106a-150">Yük dengeleme</span><span class="sxs-lookup"><span data-stu-id="8106a-150">Load balancing</span></span>

<span data-ttu-id="8106a-151">Bazı yük dengeleyiciler gRPC ile etkili bir şekilde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8106a-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="8106a-152">L4 (taşıma) yük dengeleyiciler, TCP bağlantılarını uç noktalar arasında dağıtarak bağlantı düzeyinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8106a-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="8106a-153">Bu yaklaşım, HTTP/1.1 ile yapılan Yük Dengeleme API çağrılarının yüklenmesi için iyi bir sonuç verir.</span><span class="sxs-lookup"><span data-stu-id="8106a-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="8106a-154">HTTP/1.1 ile yapılan eşzamanlı çağrılar farklı bağlantılarda gönderilir ve çağrılar, uç noktalar arasında yük dengelemesi yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="8106a-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="8106a-155">L4 yük dengeleyiciler bir bağlantı düzeyinde çalıştığından, gRPC ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8106a-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="8106a-156">gRPC, tek bir TCP bağlantısında birden çok çağrının çoğullanmış HTTP/2 kullanır.</span><span class="sxs-lookup"><span data-stu-id="8106a-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="8106a-157">Bu bağlantı üzerindeki tüm gRPC çağrıları bir uç noktaya gider.</span><span class="sxs-lookup"><span data-stu-id="8106a-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="8106a-158">GRPC 'nin yükünü etkin şekilde dengelemek için iki seçenek vardır:</span><span class="sxs-lookup"><span data-stu-id="8106a-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="8106a-159">İstemci tarafı Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="8106a-159">Client-side load balancing</span></span>
* <span data-ttu-id="8106a-160">L7 (uygulama) proxy Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="8106a-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="8106a-161">Yalnızca gRPC çağrıları uç noktalar arasında yük dengelenebilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="8106a-162">Akış gRPC çağrısı kurulduktan sonra, akış üzerinden gönderilen tüm iletiler bir uç noktaya gider.</span><span class="sxs-lookup"><span data-stu-id="8106a-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="8106a-163">İstemci tarafı Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="8106a-163">Client-side load balancing</span></span>

<span data-ttu-id="8106a-164">İstemci tarafı yük dengelemesi ile istemci, uç noktalar hakkında bilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="8106a-165">Her gRPC çağrısı için, çağrısını göndermek üzere farklı bir uç nokta seçer.</span><span class="sxs-lookup"><span data-stu-id="8106a-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="8106a-166">Gecikme süresi önemli olduğunda istemci tarafı yük dengelemesi iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="8106a-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="8106a-167">İstemci ile hizmet arasında, çağrının hizmete doğrudan gönderilmesi için bir ara sunucu yok.</span><span class="sxs-lookup"><span data-stu-id="8106a-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="8106a-168">İstemci tarafı yük dengelemenin dezavantajı, her istemcinin kullanması gereken kullanılabilir uç noktaları izlemesine sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8106a-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="8106a-169">Arabelleği istemci yük dengelemesi, Yük Dengeleme durumunun merkezi bir konumda depolandığı bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="8106a-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="8106a-170">İstemciler, Yük Dengeleme kararları verirken kullanılacak bilgiler için merkezi konumu düzenli aralıklarla sorgular.</span><span class="sxs-lookup"><span data-stu-id="8106a-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="8106a-171">`Grpc.Net.Client` Şu anda istemci tarafı yük dengelemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="8106a-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="8106a-172">.NET ' te istemci tarafı yük dengelemesi gerekliyse, [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="8106a-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="8106a-173">Proxy yük dengelemesi</span><span class="sxs-lookup"><span data-stu-id="8106a-173">Proxy load balancing</span></span>

<span data-ttu-id="8106a-174">Bir L7 (uygulama) proxy 'si, bir L4 (aktarım) proxy 'si tarafından daha yüksek bir düzeyde çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8106a-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="8106a-175">L7 proxy 'leri HTTP/2 ' yi anlayın ve birden fazla uç nokta genelinde tek bir HTTP/2 bağlantısı üzerinde gRPC çağrılarını bir tane sunucuya dağıtabiliyor.</span><span class="sxs-lookup"><span data-stu-id="8106a-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="8106a-176">Proxy kullanmak, istemci tarafı yük dengelemeden daha basittir, ancak gRPC çağrılarına ek gecikme süresi ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="8106a-177">Kullanılabilir çok sayıda L7 proxy vardır.</span><span class="sxs-lookup"><span data-stu-id="8106a-177">There are many L7 proxies available.</span></span> <span data-ttu-id="8106a-178">Bazı seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8106a-178">Some options are:</span></span>

* <span data-ttu-id="8106a-179">[Envoy](https://www.envoyproxy.io/) -popüler bir açık kaynak proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="8106a-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="8106a-180">Kubernetes için [Linkerd](https://linkerd.io/) -hizmet ağı.</span><span class="sxs-lookup"><span data-stu-id="8106a-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="8106a-181">[Yarp: bir ters ara sunucu](https://microsoft.github.io/reverse-proxy/) -.net ' te yazılmış bir önizleme açık kaynak proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="8106a-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="8106a-182">Canlı ping pingleri tut</span><span class="sxs-lookup"><span data-stu-id="8106a-182">Keep alive pings</span></span>

<span data-ttu-id="8106a-183">Canlı tut ping işlemleri, etkin olmama süreleri boyunca HTTP/2 bağlantılarının etkin kalmasını sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-183">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="8106a-184">Mevcut bir HTTP/2 bağlantısının, bir uygulamanın etkinliği sürdürülürse, yeniden yüklenmekte olan bağlantının neden olduğu bir gecikme süresi olmadan, ilk gRPC çağrılarının hızla yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="8106a-184">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="8106a-185">Etkin tutma pingleri şu şekilde yapılandırılır <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="8106a-185">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="8106a-186">Yukarıdaki kod, işlem yapılmayan dönemler sırasında her 60 saniyede sunucuya canlı tutmayı canlı tut iletisi gönderen bir kanalı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="8106a-186">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="8106a-187">Ping işlemi, sunucunun ve kullanımda olan tüm proxy 'lerin, etkin olmama nedeniyle bağlantıyı kapatmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="8106a-187">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="8106a-188">Akış</span><span class="sxs-lookup"><span data-stu-id="8106a-188">Streaming</span></span>

<span data-ttu-id="8106a-189">gRPC çift yönlü akışı, yüksek performanslı senaryolarda birli gRPC çağrılarını değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-189">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="8106a-190">Çift yönlü bir akış başlatıldıktan sonra, akış iletileri geri ve ileri, birden çok birli gRPC çağrısı içeren iletiler göndermekten daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="8106a-190">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="8106a-191">Akışlı iletiler, var olan bir HTTP/2 isteğine veri olarak gönderilir ve her birli çağrı için yeni bir HTTP/2 isteği oluşturma yükünü ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="8106a-191">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="8106a-192">Örnek hizmet:</span><span class="sxs-lookup"><span data-stu-id="8106a-192">Example service:</span></span>

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

<span data-ttu-id="8106a-193">Örnek istemci:</span><span class="sxs-lookup"><span data-stu-id="8106a-193">Example client:</span></span>

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

<span data-ttu-id="8106a-194">Çift yönlü akışla birli çağrıları performans nedenleriyle değiştirmek, gelişmiş bir tekniktir ve birçok durumda uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="8106a-194">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="8106a-195">Akış çağrılarının kullanılması şu durumlarda iyi bir seçimdir:</span><span class="sxs-lookup"><span data-stu-id="8106a-195">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="8106a-196">Yüksek aktarım hızı veya düşük gecikme süresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8106a-196">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="8106a-197">gRPC ve HTTP/2 performans sorunu olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="8106a-197">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="8106a-198">İstemcideki bir çalışan, gRPC hizmeti ile normal iletiler gönderiyor veya alıyor.</span><span class="sxs-lookup"><span data-stu-id="8106a-198">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="8106a-199">Birli yerine akış çağrılarını kullanmanın ek karmaşıklığı ve sınırlamalarından haberdar olun:</span><span class="sxs-lookup"><span data-stu-id="8106a-199">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="8106a-200">Bir akış, bir hizmet veya bağlantı hatası tarafından kesintiye uğrar.</span><span class="sxs-lookup"><span data-stu-id="8106a-200">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="8106a-201">Bir hata varsa akışın yeniden başlatılması için mantık gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8106a-201">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="8106a-202">`RequestStream.WriteAsync` Çoklu iş parçacığı oluşturma için güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="8106a-202">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="8106a-203">Tek seferde bir akışa yalnızca bir ileti yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-203">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="8106a-204">Tek bir akış üzerinden birden çok iş parçacığından ileti göndermek için, Marshall iletileri gibi bir üretici/tüketici kuyruğu gerekir <xref:System.Threading.Channels.Channel%601> .</span><span class="sxs-lookup"><span data-stu-id="8106a-204">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="8106a-205">Bir gRPC akış yöntemi bir ileti türü alacak ve bir ileti türü göndermesiyle sınırlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8106a-205">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="8106a-206">Örneğin, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` alır `RequestMessage` ve gönderir `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="8106a-206">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="8106a-207">Ve kullanılarak bilinmeyen veya koşullu iletiler için protoarabellek desteği `Any` `oneof` Bu sınırlamaya geçici çözüm verebilir.</span><span class="sxs-lookup"><span data-stu-id="8106a-207">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
