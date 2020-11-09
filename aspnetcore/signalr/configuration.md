---
title: 'ASP.NET Core SignalR yapılandırması'
author: bradygaster
description: 'ASP.NET Core uygulamalarını nasıl yapılandıracağınızı öğrenin SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061242"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="9a678-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9a678-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a678-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a678-105">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9a678-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a678-106">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a678-107">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a678-108">`AddJsonProtocol`, [eklentisi eklendikten sonra SignalR eklenebilir](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a678-109">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="9a678-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a678-110">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a678-111">Daha fazla bilgi için, [ belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a678-112">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a678-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9a678-113">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9a678-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a678-114">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9a678-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9a678-115">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a678-116">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="9a678-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a678-117">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9a678-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a678-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-118">MessagePack serialization options</span></span>

<span data-ttu-id="9a678-119">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a678-120">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9a678-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-121">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a678-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-122">Configure server options</span></span>

<span data-ttu-id="9a678-123">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a678-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a678-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-124">Option</span></span> | <span data-ttu-id="9a678-125">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-125">Default Value</span></span> | <span data-ttu-id="9a678-126">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a678-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-127">30 seconds</span></span> | <span data-ttu-id="9a678-128">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a678-129">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a678-130">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a678-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-131">15 seconds</span></span> | <span data-ttu-id="9a678-132">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a678-133">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-134">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-135">15 seconds</span></span> | <span data-ttu-id="9a678-136">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a678-137">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9a678-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a678-138">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a678-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9a678-139">All installed protocols</span></span> | <span data-ttu-id="9a678-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9a678-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9a678-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a678-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a678-142">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9a678-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a678-143">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a678-144">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a678-145">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a678-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-146">32 KB</span></span> | <span data-ttu-id="9a678-147">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9a678-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="9a678-148">1</span><span class="sxs-lookup"><span data-stu-id="9a678-148">1</span></span> | <span data-ttu-id="9a678-149">Kuyruğa almadan önce her bir istemcinin paralel olarak çağırabilmesi için en fazla hub yöntemi sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="9a678-150">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="9a678-151">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="9a678-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a678-152">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a678-153">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a678-154">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9a678-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="9a678-155">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9a678-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a678-156">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-156">Option</span></span> | <span data-ttu-id="9a678-157">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-157">Default Value</span></span> | <span data-ttu-id="9a678-158">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a678-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-159">32 KB</span></span> | <span data-ttu-id="9a678-160">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a678-161">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a678-162">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a678-163">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9a678-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a678-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-164">32 KB</span></span> | <span data-ttu-id="9a678-165">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a678-166">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a678-167">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9a678-167">All Transports are enabled.</span></span> | <span data-ttu-id="9a678-168">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9a678-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a678-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-169">See below.</span></span> | <span data-ttu-id="9a678-170">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a678-171">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-171">See below.</span></span> | <span data-ttu-id="9a678-172">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9a678-173">0</span><span class="sxs-lookup"><span data-stu-id="9a678-173">0</span></span> | <span data-ttu-id="9a678-174">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9a678-175">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9a678-176">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="9a678-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a678-177">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-177">Option</span></span> | <span data-ttu-id="9a678-178">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-178">Default Value</span></span> | <span data-ttu-id="9a678-179">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a678-180">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-180">90 seconds</span></span> | <span data-ttu-id="9a678-181">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a678-182">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9a678-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a678-183">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="9a678-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a678-184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-184">Option</span></span> | <span data-ttu-id="9a678-185">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-185">Default Value</span></span> | <span data-ttu-id="9a678-186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a678-187">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-187">5 seconds</span></span> | <span data-ttu-id="9a678-188">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a678-189">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="9a678-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a678-190">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a678-191">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-191">Configure client options</span></span>

<span data-ttu-id="9a678-192">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9a678-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a678-193">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="9a678-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a678-194">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-194">Configure logging</span></span>

<span data-ttu-id="9a678-195">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="9a678-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a678-196">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a678-197">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-198">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a678-199">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a678-200">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a678-201">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9a678-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a678-202">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a678-203">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9a678-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a678-204">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a678-205">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a678-206">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="9a678-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a678-207">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-207">The following table lists the available log levels.</span></span> <span data-ttu-id="9a678-208">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a678-209">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler** günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="9a678-210">Dize</span><span class="sxs-lookup"><span data-stu-id="9a678-210">String</span></span>                      | <span data-ttu-id="9a678-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a678-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a678-212">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9a678-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a678-213">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a678-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a678-214">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a678-215">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9a678-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a678-216">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a678-217">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a678-218">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a678-219">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9a678-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a678-220">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a678-221">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-221">Configure allowed transports</span></span>

<span data-ttu-id="9a678-222">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a678-223">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a678-224">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9a678-224">All transports are enabled by default.</span></span>

<span data-ttu-id="9a678-225">Örneğin, Server-Sent olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9a678-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a678-226">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a678-227">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9a678-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a678-228">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9a678-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a678-229">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9a678-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a678-230">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9a678-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a678-231">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-231">Configure bearer authentication</span></span>

<span data-ttu-id="9a678-232">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a678-233">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="9a678-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a678-234">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de Server-Sent olayları ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a678-235">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="9a678-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a678-236">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a678-237">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="9a678-238">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a678-239">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a678-240">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a678-241">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9a678-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a678-242">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9a678-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-243">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-244">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-244">Option</span></span> | <span data-ttu-id="9a678-245">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-245">Default value</span></span> | <span data-ttu-id="9a678-246">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a678-247">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-248">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-248">Timeout for server activity.</span></span> <span data-ttu-id="9a678-249">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-250">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-251">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a678-252">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-252">15 seconds</span></span> | <span data-ttu-id="9a678-253">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-254">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-255">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-256">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-257">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-257">15 seconds</span></span> | <span data-ttu-id="9a678-258">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-259">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-260">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a678-261">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="9a678-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a678-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-263">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-263">Option</span></span> | <span data-ttu-id="9a678-264">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-264">Default value</span></span> | <span data-ttu-id="9a678-265">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a678-266">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-267">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-267">Timeout for server activity.</span></span> <span data-ttu-id="9a678-268">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a678-269">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-270">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a678-271">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-272">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-273">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-274">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-275">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-275">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-276">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-276">Option</span></span> | <span data-ttu-id="9a678-277">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-277">Default value</span></span> | <span data-ttu-id="9a678-278">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a678-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a678-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a678-280">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-281">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-281">Timeout for server activity.</span></span> <span data-ttu-id="9a678-282">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-283">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-284">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a678-285">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-285">15 seconds</span></span> | <span data-ttu-id="9a678-286">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-287">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-288">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-289">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a678-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a678-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a678-291">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-292">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-293">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-294">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a678-295">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-295">Configure additional options</span></span>

<span data-ttu-id="9a678-296">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9a678-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-297">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-298">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-298">.NET Option</span></span> |  <span data-ttu-id="9a678-299">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-299">Default value</span></span> | <span data-ttu-id="9a678-300">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a678-301">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a678-302">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-303">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-304">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a678-305">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-305">Empty</span></span> | <span data-ttu-id="9a678-306">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a678-307">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-307">Empty</span></span> | <span data-ttu-id="9a678-308">cookieHer HTTP isteğiyle gönderilmek üzere BIR http s koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a678-309">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-309">Empty</span></span> | <span data-ttu-id="9a678-310">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9a678-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a678-311">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-311">5 seconds</span></span> | <span data-ttu-id="9a678-312">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9a678-312">WebSockets only.</span></span> <span data-ttu-id="9a678-313">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a678-314">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9a678-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a678-315">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-315">Empty</span></span> | <span data-ttu-id="9a678-316">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a678-317">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="9a678-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a678-318">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9a678-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a678-319">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a678-320">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9a678-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a678-321">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (örneğin, Cookie s ve üstbilgiler) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9a678-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a678-322">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9a678-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a678-323">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a678-324">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9a678-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a678-325">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9a678-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a678-326">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a678-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-328">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-328">JavaScript Option</span></span> | <span data-ttu-id="9a678-329">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-329">Default Value</span></span> | <span data-ttu-id="9a678-330">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a678-331">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="9a678-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Bağlantı için kullanılacak aktarımı belirten bir değer.</span><span class="sxs-lookup"><span data-stu-id="9a678-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="9a678-333">Her HTTP isteğiyle gönderilen üstbilgilerin sözlüğü.</span><span class="sxs-lookup"><span data-stu-id="9a678-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="9a678-334">Üst bilgilerin tarayıcıda gönderilmesi WebSockets veya Stream için çalışmaz <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> .</span><span class="sxs-lookup"><span data-stu-id="9a678-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="9a678-335">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a678-336">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-337">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-338">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="9a678-339">CORS isteğiyle kimlik bilgilerinin gönderilip gönderilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="9a678-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="9a678-340">Azure App Service cookie , yapışkan oturum için s kullanır ve bu seçeneğin doğru şekilde çalışmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9a678-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="9a678-341">CORS hakkında daha fazla bilgi için SignalR bkz <xref:signalr/security#cross-origin-resource-sharing> ..</span><span class="sxs-lookup"><span data-stu-id="9a678-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-342">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-342">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-343">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-343">Java Option</span></span> | <span data-ttu-id="9a678-344">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-344">Default Value</span></span> | <span data-ttu-id="9a678-345">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a678-346">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a678-347">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-348">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-349">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a678-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a678-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a678-351">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-351">Empty</span></span> | <span data-ttu-id="9a678-352">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a678-353">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a678-354">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="9a678-355">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a678-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a678-356">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a678-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a678-357">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a678-358">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9a678-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a678-359">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a678-360">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a678-361">`AddJsonProtocol`, [eklentisi eklendikten sonra SignalR eklenebilir](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a678-362">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="9a678-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a678-363">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a678-364">Daha fazla bilgi için, [ belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a678-365">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a678-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9a678-366">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9a678-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a678-367">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9a678-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9a678-368">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a678-369">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="9a678-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a678-370">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9a678-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a678-371">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-371">MessagePack serialization options</span></span>

<span data-ttu-id="9a678-372">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a678-373">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9a678-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-374">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a678-375">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-375">Configure server options</span></span>

<span data-ttu-id="9a678-376">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a678-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a678-377">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-377">Option</span></span> | <span data-ttu-id="9a678-378">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-378">Default Value</span></span> | <span data-ttu-id="9a678-379">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a678-380">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-380">30 seconds</span></span> | <span data-ttu-id="9a678-381">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a678-382">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a678-383">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a678-384">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-384">15 seconds</span></span> | <span data-ttu-id="9a678-385">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a678-386">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-387">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-388">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-388">15 seconds</span></span> | <span data-ttu-id="9a678-389">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a678-390">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9a678-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a678-391">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a678-392">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9a678-392">All installed protocols</span></span> | <span data-ttu-id="9a678-393">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9a678-393">Protocols supported by this hub.</span></span> <span data-ttu-id="9a678-394">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a678-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a678-395">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9a678-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a678-396">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a678-397">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a678-398">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a678-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-399">32 KB</span></span> | <span data-ttu-id="9a678-400">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9a678-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9a678-401">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="9a678-402">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="9a678-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a678-403">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a678-404">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a678-405">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9a678-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="9a678-406">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9a678-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a678-407">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-407">Option</span></span> | <span data-ttu-id="9a678-408">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-408">Default Value</span></span> | <span data-ttu-id="9a678-409">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a678-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-410">32 KB</span></span> | <span data-ttu-id="9a678-411">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a678-412">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a678-413">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a678-414">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9a678-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a678-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-415">32 KB</span></span> | <span data-ttu-id="9a678-416">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a678-417">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a678-418">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9a678-418">All Transports are enabled.</span></span> | <span data-ttu-id="9a678-419">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9a678-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a678-420">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-420">See below.</span></span> | <span data-ttu-id="9a678-421">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a678-422">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-422">See below.</span></span> | <span data-ttu-id="9a678-423">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9a678-424">0</span><span class="sxs-lookup"><span data-stu-id="9a678-424">0</span></span> | <span data-ttu-id="9a678-425">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9a678-426">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9a678-427">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="9a678-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a678-428">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-428">Option</span></span> | <span data-ttu-id="9a678-429">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-429">Default Value</span></span> | <span data-ttu-id="9a678-430">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a678-431">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-431">90 seconds</span></span> | <span data-ttu-id="9a678-432">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a678-433">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9a678-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a678-434">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="9a678-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a678-435">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-435">Option</span></span> | <span data-ttu-id="9a678-436">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-436">Default Value</span></span> | <span data-ttu-id="9a678-437">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a678-438">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-438">5 seconds</span></span> | <span data-ttu-id="9a678-439">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a678-440">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="9a678-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a678-441">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a678-442">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-442">Configure client options</span></span>

<span data-ttu-id="9a678-443">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9a678-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a678-444">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="9a678-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a678-445">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-445">Configure logging</span></span>

<span data-ttu-id="9a678-446">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="9a678-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a678-447">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a678-448">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-449">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a678-450">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a678-451">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a678-452">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9a678-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a678-453">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a678-454">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9a678-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a678-455">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a678-456">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a678-457">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="9a678-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a678-458">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-458">The following table lists the available log levels.</span></span> <span data-ttu-id="9a678-459">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a678-460">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler** günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="9a678-461">Dize</span><span class="sxs-lookup"><span data-stu-id="9a678-461">String</span></span>                      | <span data-ttu-id="9a678-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a678-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a678-463">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9a678-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a678-464">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a678-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a678-465">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a678-466">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9a678-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a678-467">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a678-468">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a678-469">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a678-470">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9a678-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a678-471">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a678-472">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-472">Configure allowed transports</span></span>

<span data-ttu-id="9a678-473">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a678-474">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a678-475">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9a678-475">All transports are enabled by default.</span></span>

<span data-ttu-id="9a678-476">Örneğin, Server-Sent olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9a678-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a678-477">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a678-478">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9a678-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a678-479">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9a678-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a678-480">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9a678-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a678-481">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9a678-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a678-482">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-482">Configure bearer authentication</span></span>

<span data-ttu-id="9a678-483">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a678-484">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="9a678-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a678-485">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de Server-Sent olayları ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a678-486">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="9a678-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a678-487">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a678-488">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="9a678-489">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a678-490">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a678-491">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a678-492">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9a678-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a678-493">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9a678-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-494">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-495">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-495">Option</span></span> | <span data-ttu-id="9a678-496">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-496">Default value</span></span> | <span data-ttu-id="9a678-497">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a678-498">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-499">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-499">Timeout for server activity.</span></span> <span data-ttu-id="9a678-500">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-501">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-502">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a678-503">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-503">15 seconds</span></span> | <span data-ttu-id="9a678-504">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-505">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-506">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-507">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-508">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-508">15 seconds</span></span> | <span data-ttu-id="9a678-509">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-510">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-511">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a678-512">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="9a678-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a678-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-514">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-514">Option</span></span> | <span data-ttu-id="9a678-515">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-515">Default value</span></span> | <span data-ttu-id="9a678-516">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a678-517">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-518">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-518">Timeout for server activity.</span></span> <span data-ttu-id="9a678-519">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a678-520">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-521">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a678-522">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-523">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-524">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-525">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-526">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-526">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-527">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-527">Option</span></span> | <span data-ttu-id="9a678-528">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-528">Default value</span></span> | <span data-ttu-id="9a678-529">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a678-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a678-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a678-531">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-532">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-532">Timeout for server activity.</span></span> <span data-ttu-id="9a678-533">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-534">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-535">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a678-536">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-536">15 seconds</span></span> | <span data-ttu-id="9a678-537">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-538">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-539">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-540">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a678-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a678-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a678-542">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-543">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-544">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-545">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a678-546">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-546">Configure additional options</span></span>

<span data-ttu-id="9a678-547">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9a678-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-548">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-549">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-549">.NET Option</span></span> |  <span data-ttu-id="9a678-550">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-550">Default value</span></span> | <span data-ttu-id="9a678-551">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a678-552">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a678-553">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-554">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-555">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a678-556">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-556">Empty</span></span> | <span data-ttu-id="9a678-557">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a678-558">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-558">Empty</span></span> | <span data-ttu-id="9a678-559">cookieHer HTTP isteğiyle gönderilmek üzere BIR http s koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a678-560">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-560">Empty</span></span> | <span data-ttu-id="9a678-561">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9a678-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a678-562">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-562">5 seconds</span></span> | <span data-ttu-id="9a678-563">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9a678-563">WebSockets only.</span></span> <span data-ttu-id="9a678-564">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a678-565">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9a678-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a678-566">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-566">Empty</span></span> | <span data-ttu-id="9a678-567">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a678-568">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="9a678-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a678-569">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9a678-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a678-570">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a678-571">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9a678-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a678-572">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (örneğin, Cookie s ve üstbilgiler) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9a678-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a678-573">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9a678-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a678-574">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a678-575">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9a678-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a678-576">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9a678-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a678-577">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a678-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-579">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-579">JavaScript Option</span></span> | <span data-ttu-id="9a678-580">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-580">Default Value</span></span> | <span data-ttu-id="9a678-581">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a678-582">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="9a678-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Bağlantı için kullanılacak aktarımı belirten bir değer.</span><span class="sxs-lookup"><span data-stu-id="9a678-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="9a678-584">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a678-585">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-586">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-587">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-588">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-588">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-589">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-589">Java Option</span></span> | <span data-ttu-id="9a678-590">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-590">Default Value</span></span> | <span data-ttu-id="9a678-591">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a678-592">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a678-593">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-594">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-595">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a678-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a678-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a678-597">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-597">Empty</span></span> | <span data-ttu-id="9a678-598">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a678-599">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a678-600">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a678-601">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a678-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a678-602">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a678-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a678-603">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a678-604">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9a678-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a678-605">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a678-606">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a678-607">`AddJsonProtocol`, [eklentisi eklendikten sonra SignalR eklenebilir](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a678-608">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="9a678-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a678-609">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a678-610">Daha fazla bilgi için, [ belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a678-611">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a678-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9a678-612">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9a678-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a678-613">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9a678-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9a678-614">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a678-615">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="9a678-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a678-616">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9a678-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a678-617">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-617">MessagePack serialization options</span></span>

<span data-ttu-id="9a678-618">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a678-619">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9a678-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-620">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a678-621">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-621">Configure server options</span></span>

<span data-ttu-id="9a678-622">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a678-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a678-623">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-623">Option</span></span> | <span data-ttu-id="9a678-624">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-624">Default Value</span></span> | <span data-ttu-id="9a678-625">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a678-626">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-626">30 seconds</span></span> | <span data-ttu-id="9a678-627">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a678-628">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a678-629">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a678-630">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-630">15 seconds</span></span> | <span data-ttu-id="9a678-631">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a678-632">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-633">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-634">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-634">15 seconds</span></span> | <span data-ttu-id="9a678-635">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a678-636">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9a678-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a678-637">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a678-638">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9a678-638">All installed protocols</span></span> | <span data-ttu-id="9a678-639">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9a678-639">Protocols supported by this hub.</span></span> <span data-ttu-id="9a678-640">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a678-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a678-641">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9a678-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a678-642">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a678-643">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a678-644">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a678-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-645">32 KB</span></span> | <span data-ttu-id="9a678-646">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9a678-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9a678-647">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="9a678-648">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="9a678-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a678-649">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a678-650">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a678-651">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9a678-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="9a678-652">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9a678-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a678-653">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-653">Option</span></span> | <span data-ttu-id="9a678-654">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-654">Default Value</span></span> | <span data-ttu-id="9a678-655">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a678-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-656">32 KB</span></span> | <span data-ttu-id="9a678-657">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a678-658">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a678-659">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a678-660">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9a678-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a678-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-661">32 KB</span></span> | <span data-ttu-id="9a678-662">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a678-663">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a678-664">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9a678-664">All Transports are enabled.</span></span> | <span data-ttu-id="9a678-665">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9a678-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a678-666">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-666">See below.</span></span> | <span data-ttu-id="9a678-667">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a678-668">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-668">See below.</span></span> | <span data-ttu-id="9a678-669">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a678-670">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="9a678-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a678-671">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-671">Option</span></span> | <span data-ttu-id="9a678-672">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-672">Default Value</span></span> | <span data-ttu-id="9a678-673">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a678-674">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-674">90 seconds</span></span> | <span data-ttu-id="9a678-675">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a678-676">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9a678-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a678-677">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="9a678-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a678-678">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-678">Option</span></span> | <span data-ttu-id="9a678-679">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-679">Default Value</span></span> | <span data-ttu-id="9a678-680">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a678-681">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-681">5 seconds</span></span> | <span data-ttu-id="9a678-682">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a678-683">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="9a678-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a678-684">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a678-685">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-685">Configure client options</span></span>

<span data-ttu-id="9a678-686">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9a678-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a678-687">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="9a678-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a678-688">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-688">Configure logging</span></span>

<span data-ttu-id="9a678-689">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="9a678-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a678-690">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a678-691">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-692">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a678-693">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a678-694">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a678-695">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9a678-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a678-696">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a678-697">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9a678-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a678-698">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a678-699">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a678-700">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="9a678-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a678-701">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9a678-701">The following table lists the available log levels.</span></span> <span data-ttu-id="9a678-702">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a678-703">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler** günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="9a678-704">Dize</span><span class="sxs-lookup"><span data-stu-id="9a678-704">String</span></span>                      | <span data-ttu-id="9a678-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a678-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a678-706">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9a678-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a678-707">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a678-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a678-708">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a678-709">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9a678-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a678-710">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a678-711">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a678-712">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a678-713">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9a678-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a678-714">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a678-715">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-715">Configure allowed transports</span></span>

<span data-ttu-id="9a678-716">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a678-717">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a678-718">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9a678-718">All transports are enabled by default.</span></span>

<span data-ttu-id="9a678-719">Örneğin, Server-Sent olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9a678-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a678-720">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a678-721">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9a678-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a678-722">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9a678-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a678-723">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9a678-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a678-724">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9a678-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a678-725">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-725">Configure bearer authentication</span></span>

<span data-ttu-id="9a678-726">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a678-727">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="9a678-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a678-728">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de Server-Sent olayları ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a678-729">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="9a678-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a678-730">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a678-731">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="9a678-732">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a678-733">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a678-734">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a678-735">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9a678-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a678-736">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9a678-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-737">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-738">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-738">Option</span></span> | <span data-ttu-id="9a678-739">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-739">Default value</span></span> | <span data-ttu-id="9a678-740">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a678-741">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-742">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-742">Timeout for server activity.</span></span> <span data-ttu-id="9a678-743">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-744">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-745">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a678-746">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-746">15 seconds</span></span> | <span data-ttu-id="9a678-747">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-748">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-749">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-750">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-751">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-751">15 seconds</span></span> | <span data-ttu-id="9a678-752">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-753">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-754">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a678-755">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="9a678-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a678-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-757">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-757">Option</span></span> | <span data-ttu-id="9a678-758">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-758">Default value</span></span> | <span data-ttu-id="9a678-759">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a678-760">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-761">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-761">Timeout for server activity.</span></span> <span data-ttu-id="9a678-762">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a678-763">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-764">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a678-765">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-766">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-767">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-768">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-769">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-769">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-770">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-770">Option</span></span> | <span data-ttu-id="9a678-771">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-771">Default value</span></span> | <span data-ttu-id="9a678-772">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a678-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a678-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a678-774">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-775">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-775">Timeout for server activity.</span></span> <span data-ttu-id="9a678-776">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-777">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-778">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a678-779">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-779">15 seconds</span></span> | <span data-ttu-id="9a678-780">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-781">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-782">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-783">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a678-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a678-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a678-785">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-786">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-787">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-788">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a678-789">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-789">Configure additional options</span></span>

<span data-ttu-id="9a678-790">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9a678-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-791">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-792">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-792">.NET Option</span></span> |  <span data-ttu-id="9a678-793">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-793">Default value</span></span> | <span data-ttu-id="9a678-794">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a678-795">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a678-796">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-797">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-798">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a678-799">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-799">Empty</span></span> | <span data-ttu-id="9a678-800">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a678-801">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-801">Empty</span></span> | <span data-ttu-id="9a678-802">cookieHer HTTP isteğiyle gönderilmek üzere BIR http s koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a678-803">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-803">Empty</span></span> | <span data-ttu-id="9a678-804">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9a678-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a678-805">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-805">5 seconds</span></span> | <span data-ttu-id="9a678-806">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9a678-806">WebSockets only.</span></span> <span data-ttu-id="9a678-807">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a678-808">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9a678-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a678-809">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-809">Empty</span></span> | <span data-ttu-id="9a678-810">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a678-811">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="9a678-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a678-812">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9a678-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a678-813">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a678-814">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9a678-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a678-815">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (örneğin, Cookie s ve üstbilgiler) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9a678-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a678-816">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9a678-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a678-817">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a678-818">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9a678-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a678-819">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9a678-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a678-820">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a678-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-822">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-822">JavaScript Option</span></span> | <span data-ttu-id="9a678-823">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-823">Default Value</span></span> | <span data-ttu-id="9a678-824">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a678-825">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="9a678-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Bağlantı için kullanılacak aktarımı belirten bir değer.</span><span class="sxs-lookup"><span data-stu-id="9a678-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="9a678-827">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a678-828">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-829">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-830">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-831">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-831">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-832">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-832">Java Option</span></span> | <span data-ttu-id="9a678-833">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-833">Default Value</span></span> | <span data-ttu-id="9a678-834">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a678-835">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a678-836">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-837">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-838">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a678-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a678-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a678-840">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-840">Empty</span></span> | <span data-ttu-id="9a678-841">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a678-842">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a678-843">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a678-844">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a678-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a678-845">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a678-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a678-846">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a678-847">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9a678-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a678-848">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a678-849">JSON serileştirme, metotta [eklendikten sonra SignalR eklenebilecek](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a678-850">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="9a678-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a678-851">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a678-852">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9a678-853">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a678-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9a678-854">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9a678-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a678-855">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9a678-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9a678-856">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a678-857">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-857">MessagePack serialization options</span></span>

<span data-ttu-id="9a678-858">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a678-859">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9a678-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-860">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a678-861">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-861">Configure server options</span></span>

<span data-ttu-id="9a678-862">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a678-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a678-863">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-863">Option</span></span> | <span data-ttu-id="9a678-864">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-864">Default Value</span></span> | <span data-ttu-id="9a678-865">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a678-866">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-866">30 seconds</span></span> | <span data-ttu-id="9a678-867">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a678-868">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a678-869">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a678-870">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-870">15 seconds</span></span> | <span data-ttu-id="9a678-871">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a678-872">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-873">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-874">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-874">15 seconds</span></span> | <span data-ttu-id="9a678-875">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a678-876">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9a678-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a678-877">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a678-878">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9a678-878">All installed protocols</span></span> | <span data-ttu-id="9a678-879">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9a678-879">Protocols supported by this hub.</span></span> <span data-ttu-id="9a678-880">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a678-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a678-881">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9a678-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a678-882">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9a678-883">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="9a678-884">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="9a678-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a678-885">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a678-886">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a678-887">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9a678-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="9a678-888">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9a678-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a678-889">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-889">Option</span></span> | <span data-ttu-id="9a678-890">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-890">Default Value</span></span> | <span data-ttu-id="9a678-891">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a678-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-892">32 KB</span></span> | <span data-ttu-id="9a678-893">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9a678-894">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a678-895">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a678-896">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9a678-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a678-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-897">32 KB</span></span> | <span data-ttu-id="9a678-898">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9a678-899">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a678-900">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9a678-900">All Transports are enabled.</span></span> | <span data-ttu-id="9a678-901">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9a678-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a678-902">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-902">See below.</span></span> | <span data-ttu-id="9a678-903">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a678-904">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-904">See below.</span></span> | <span data-ttu-id="9a678-905">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a678-906">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="9a678-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a678-907">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-907">Option</span></span> | <span data-ttu-id="9a678-908">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-908">Default Value</span></span> | <span data-ttu-id="9a678-909">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a678-910">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-910">90 seconds</span></span> | <span data-ttu-id="9a678-911">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a678-912">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9a678-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a678-913">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="9a678-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a678-914">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-914">Option</span></span> | <span data-ttu-id="9a678-915">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-915">Default Value</span></span> | <span data-ttu-id="9a678-916">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a678-917">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-917">5 seconds</span></span> | <span data-ttu-id="9a678-918">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a678-919">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="9a678-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a678-920">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a678-921">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-921">Configure client options</span></span>

<span data-ttu-id="9a678-922">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9a678-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a678-923">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="9a678-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a678-924">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-924">Configure logging</span></span>

<span data-ttu-id="9a678-925">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="9a678-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a678-926">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a678-927">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-928">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a678-929">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a678-930">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a678-931">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9a678-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a678-932">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a678-933">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9a678-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a678-934">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a678-935">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a678-936">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9a678-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a678-937">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a678-938">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a678-939">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a678-940">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9a678-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a678-941">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a678-942">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-942">Configure allowed transports</span></span>

<span data-ttu-id="9a678-943">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a678-944">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a678-945">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9a678-945">All transports are enabled by default.</span></span>

<span data-ttu-id="9a678-946">Örneğin, Server-Sent olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9a678-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a678-947">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a678-948">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9a678-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a678-949">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-949">Configure bearer authentication</span></span>

<span data-ttu-id="9a678-950">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a678-951">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="9a678-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a678-952">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de Server-Sent olayları ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a678-953">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="9a678-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a678-954">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a678-955">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="9a678-956">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a678-957">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a678-958">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a678-959">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9a678-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a678-960">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9a678-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-961">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-962">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-962">Option</span></span> | <span data-ttu-id="9a678-963">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-963">Default value</span></span> | <span data-ttu-id="9a678-964">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a678-965">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-966">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-966">Timeout for server activity.</span></span> <span data-ttu-id="9a678-967">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-968">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-969">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a678-970">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-970">15 seconds</span></span> | <span data-ttu-id="9a678-971">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-972">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-973">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-974">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-975">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-975">15 seconds</span></span> | <span data-ttu-id="9a678-976">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-977">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-978">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a678-979">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="9a678-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a678-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-981">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-981">Option</span></span> | <span data-ttu-id="9a678-982">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-982">Default value</span></span> | <span data-ttu-id="9a678-983">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a678-984">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-985">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-985">Timeout for server activity.</span></span> <span data-ttu-id="9a678-986">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a678-987">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-988">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a678-989">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-990">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-991">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-992">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-993">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-993">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-994">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-994">Option</span></span> | <span data-ttu-id="9a678-995">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-995">Default value</span></span> | <span data-ttu-id="9a678-996">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a678-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a678-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a678-998">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-999">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-999">Timeout for server activity.</span></span> <span data-ttu-id="9a678-1000">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-1001">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-1002">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a678-1003">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1003">15 seconds</span></span> | <span data-ttu-id="9a678-1004">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-1005">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-1006">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-1007">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a678-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a678-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a678-1009">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a678-1010">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a678-1011">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a678-1012">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9a678-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a678-1013">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1013">Configure additional options</span></span>

<span data-ttu-id="9a678-1014">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-1016">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1016">.NET Option</span></span> |  <span data-ttu-id="9a678-1017">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1017">Default value</span></span> | <span data-ttu-id="9a678-1018">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a678-1019">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a678-1020">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1021">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1022">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a678-1023">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1023">Empty</span></span> | <span data-ttu-id="9a678-1024">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a678-1025">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1025">Empty</span></span> | <span data-ttu-id="9a678-1026">cookieHer HTTP isteğiyle gönderilmek üzere BIR http s koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a678-1027">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1027">Empty</span></span> | <span data-ttu-id="9a678-1028">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9a678-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a678-1029">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1029">5 seconds</span></span> | <span data-ttu-id="9a678-1030">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9a678-1030">WebSockets only.</span></span> <span data-ttu-id="9a678-1031">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a678-1032">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9a678-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a678-1033">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1033">Empty</span></span> | <span data-ttu-id="9a678-1034">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a678-1035">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a678-1036">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9a678-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a678-1037">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a678-1038">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9a678-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a678-1039">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (örneğin, Cookie s ve üstbilgiler) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9a678-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a678-1040">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9a678-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a678-1041">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a678-1042">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9a678-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a678-1043">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9a678-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a678-1044">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a678-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-1046">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1046">JavaScript Option</span></span> | <span data-ttu-id="9a678-1047">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1047">Default Value</span></span> | <span data-ttu-id="9a678-1048">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a678-1049">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="9a678-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Bağlantı için kullanılacak aktarımı belirten bir değer.</span><span class="sxs-lookup"><span data-stu-id="9a678-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="9a678-1051">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a678-1052">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1053">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1054">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-1055">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-1056">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1056">Java Option</span></span> | <span data-ttu-id="9a678-1057">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1057">Default Value</span></span> | <span data-ttu-id="9a678-1058">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a678-1059">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a678-1060">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1061">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1062">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a678-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a678-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a678-1064">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1064">Empty</span></span> | <span data-ttu-id="9a678-1065">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a678-1066">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a678-1067">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a678-1068">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a678-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a678-1069">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a678-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a678-1070">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a678-1071">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9a678-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a678-1072">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a678-1073">JSON serileştirme, metotta [eklendikten sonra SignalR eklenebilecek](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a678-1074">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a678-1075">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a678-1076">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9a678-1077">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9a678-1078">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9a678-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a678-1079">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9a678-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9a678-1080">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a678-1081">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-1081">MessagePack serialization options</span></span>

<span data-ttu-id="9a678-1082">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a678-1083">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="9a678-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-1084">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a678-1085">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1085">Configure server options</span></span>

<span data-ttu-id="9a678-1086">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="9a678-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a678-1087">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1087">Option</span></span> | <span data-ttu-id="9a678-1088">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1088">Default Value</span></span> | <span data-ttu-id="9a678-1089">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9a678-1090">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1090">15 seconds</span></span> | <span data-ttu-id="9a678-1091">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a678-1092">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-1093">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a678-1094">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1094">15 seconds</span></span> | <span data-ttu-id="9a678-1095">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a678-1096">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9a678-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a678-1097">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a678-1098">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9a678-1098">All installed protocols</span></span> | <span data-ttu-id="9a678-1099">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9a678-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="9a678-1100">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a678-1101">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9a678-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a678-1102">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9a678-1103">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="9a678-1104">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="9a678-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a678-1105">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9a678-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a678-1106">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a678-1107">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="9a678-1108">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9a678-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a678-1109">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1109">Option</span></span> | <span data-ttu-id="9a678-1110">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1110">Default Value</span></span> | <span data-ttu-id="9a678-1111">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a678-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-1112">32 KB</span></span> | <span data-ttu-id="9a678-1113">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9a678-1114">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a678-1115">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a678-1116">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9a678-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a678-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a678-1117">32 KB</span></span> | <span data-ttu-id="9a678-1118">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9a678-1119">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a678-1120">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9a678-1120">All Transports are enabled.</span></span> | <span data-ttu-id="9a678-1121">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9a678-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a678-1122">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1122">See below.</span></span> | <span data-ttu-id="9a678-1123">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a678-1124">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1124">See below.</span></span> | <span data-ttu-id="9a678-1125">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a678-1126">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a678-1127">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1127">Option</span></span> | <span data-ttu-id="9a678-1128">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1128">Default Value</span></span> | <span data-ttu-id="9a678-1129">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a678-1130">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1130">90 seconds</span></span> | <span data-ttu-id="9a678-1131">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a678-1132">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9a678-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a678-1133">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a678-1134">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1134">Option</span></span> | <span data-ttu-id="9a678-1135">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1135">Default Value</span></span> | <span data-ttu-id="9a678-1136">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a678-1137">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1137">5 seconds</span></span> | <span data-ttu-id="9a678-1138">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a678-1139">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a678-1140">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a678-1141">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1141">Configure client options</span></span>

<span data-ttu-id="9a678-1142">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9a678-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a678-1143">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a678-1144">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1144">Configure logging</span></span>

<span data-ttu-id="9a678-1145">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a678-1146">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a678-1147">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a678-1148">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a678-1149">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a678-1150">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a678-1151">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9a678-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a678-1152">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a678-1153">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9a678-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a678-1154">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a678-1155">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9a678-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a678-1156">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9a678-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a678-1157">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a678-1158">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a678-1159">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a678-1160">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9a678-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a678-1161">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a678-1162">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1162">Configure allowed transports</span></span>

<span data-ttu-id="9a678-1163">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a678-1164">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a678-1165">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9a678-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="9a678-1166">Örneğin, Server-Sent olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9a678-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a678-1167">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a678-1168">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1168">Configure bearer authentication</span></span>

<span data-ttu-id="9a678-1169">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a678-1170">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="9a678-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a678-1171">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de Server-Sent olayları ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a678-1172">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a678-1173">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a678-1174">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="9a678-1175">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a678-1176">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a678-1177">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a678-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a678-1178">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9a678-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a678-1179">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9a678-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-1181">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1181">Option</span></span> | <span data-ttu-id="9a678-1182">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1182">Default value</span></span> | <span data-ttu-id="9a678-1183">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a678-1184">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-1185">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1185">Timeout for server activity.</span></span> <span data-ttu-id="9a678-1186">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-1187">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-1188">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a678-1189">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1189">15 seconds</span></span> | <span data-ttu-id="9a678-1190">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-1191">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9a678-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a678-1192">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-1193">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9a678-1194">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a678-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-1196">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1196">Option</span></span> | <span data-ttu-id="9a678-1197">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1197">Default value</span></span> | <span data-ttu-id="9a678-1198">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a678-1199">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-1200">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1200">Timeout for server activity.</span></span> <span data-ttu-id="9a678-1201">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a678-1202">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-1203">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-1204">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-1205">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9a678-1205">Option</span></span> | <span data-ttu-id="9a678-1206">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1206">Default value</span></span> | <span data-ttu-id="9a678-1207">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a678-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a678-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a678-1209">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9a678-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a678-1210">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1210">Timeout for server activity.</span></span> <span data-ttu-id="9a678-1211">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-1212">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a678-1213">Önerilen değer, ping bir sürenin gelmesi için en az iki değerin değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a678-1214">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1214">15 seconds</span></span> | <span data-ttu-id="9a678-1215">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9a678-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a678-1216">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9a678-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a678-1217">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a678-1218">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a678-1219">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9a678-1219">Configure additional options</span></span>

<span data-ttu-id="9a678-1220">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a678-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="9a678-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a678-1222">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1222">.NET Option</span></span> |  <span data-ttu-id="9a678-1223">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1223">Default value</span></span> | <span data-ttu-id="9a678-1224">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a678-1225">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a678-1226">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1227">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1228">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a678-1229">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1229">Empty</span></span> | <span data-ttu-id="9a678-1230">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a678-1231">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1231">Empty</span></span> | <span data-ttu-id="9a678-1232">cookieHer HTTP isteğiyle gönderilmek üzere BIR http s koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9a678-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a678-1233">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1233">Empty</span></span> | <span data-ttu-id="9a678-1234">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9a678-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a678-1235">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9a678-1235">5 seconds</span></span> | <span data-ttu-id="9a678-1236">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9a678-1236">WebSockets only.</span></span> <span data-ttu-id="9a678-1237">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9a678-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a678-1238">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9a678-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a678-1239">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1239">Empty</span></span> | <span data-ttu-id="9a678-1240">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a678-1241">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="9a678-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a678-1242">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9a678-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a678-1243">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a678-1244">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9a678-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a678-1245">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (örneğin, Cookie s ve üstbilgiler) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9a678-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a678-1246">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9a678-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a678-1247">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a678-1248">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9a678-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a678-1249">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9a678-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a678-1250">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9a678-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a678-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a678-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a678-1252">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1252">JavaScript Option</span></span> | <span data-ttu-id="9a678-1253">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1253">Default Value</span></span> | <span data-ttu-id="9a678-1254">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a678-1255">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="9a678-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Bağlantı için kullanılacak aktarımı belirten bir değer.</span><span class="sxs-lookup"><span data-stu-id="9a678-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="9a678-1257">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a678-1258">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1259">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1260">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a678-1261">Java</span><span class="sxs-lookup"><span data-stu-id="9a678-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="9a678-1262">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9a678-1262">Java Option</span></span> | <span data-ttu-id="9a678-1263">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9a678-1263">Default Value</span></span> | <span data-ttu-id="9a678-1264">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9a678-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a678-1265">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9a678-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a678-1266">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9a678-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a678-1267">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir** .</span><span class="sxs-lookup"><span data-stu-id="9a678-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="9a678-1268">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9a678-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a678-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a678-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a678-1270">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9a678-1270">Empty</span></span> | <span data-ttu-id="9a678-1271">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9a678-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a678-1272">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a678-1273">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="9a678-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a678-1274">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a678-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a678-1275">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a678-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
