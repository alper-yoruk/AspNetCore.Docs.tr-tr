---
title: ASP.NET Core SignalR yapılandırması
author: bradygaster
description: ASP.NET Core uygulamalarını nasıl yapılandıracağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406842"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="84916-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="84916-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="84916-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="84916-105">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="84916-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="84916-106">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="84916-107">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="84916-108">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84916-109">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="84916-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="84916-110">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="84916-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="84916-111">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="84916-112">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84916-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="84916-113">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="84916-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="84916-114">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="84916-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="84916-115">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="84916-116">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="84916-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="84916-117">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="84916-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="84916-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-118">MessagePack serialization options</span></span>

<span data-ttu-id="84916-119">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="84916-120">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="84916-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-121">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="84916-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-122">Configure server options</span></span>

<span data-ttu-id="84916-123">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="84916-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="84916-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-124">Option</span></span> | <span data-ttu-id="84916-125">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-125">Default Value</span></span> | <span data-ttu-id="84916-126">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="84916-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-127">30 seconds</span></span> | <span data-ttu-id="84916-128">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="84916-129">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="84916-130">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="84916-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-131">15 seconds</span></span> | <span data-ttu-id="84916-132">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="84916-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="84916-133">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-134">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-135">15 seconds</span></span> | <span data-ttu-id="84916-136">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="84916-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="84916-137">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="84916-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="84916-138">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="84916-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="84916-139">All installed protocols</span></span> | <span data-ttu-id="84916-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="84916-140">Protocols supported by this hub.</span></span> <span data-ttu-id="84916-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="84916-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="84916-142">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="84916-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="84916-143">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="84916-144">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="84916-145">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="84916-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="84916-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-146">32 KB</span></span> | <span data-ttu-id="84916-147">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="84916-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="84916-148">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="84916-149">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="84916-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="84916-150">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="84916-151">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="84916-152">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="84916-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="84916-153">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="84916-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="84916-154">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-154">Option</span></span> | <span data-ttu-id="84916-155">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-155">Default Value</span></span> | <span data-ttu-id="84916-156">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="84916-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-157">32 KB</span></span> | <span data-ttu-id="84916-158">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="84916-159">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="84916-160">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="84916-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="84916-161">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="84916-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="84916-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-162">32 KB</span></span> | <span data-ttu-id="84916-163">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="84916-164">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="84916-165">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="84916-165">All Transports are enabled.</span></span> | <span data-ttu-id="84916-166">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="84916-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="84916-167">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-167">See below.</span></span> | <span data-ttu-id="84916-168">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="84916-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-169">See below.</span></span> | <span data-ttu-id="84916-170">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="84916-171">0</span><span class="sxs-lookup"><span data-stu-id="84916-171">0</span></span> | <span data-ttu-id="84916-172">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="84916-173">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="84916-174">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="84916-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="84916-175">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-175">Option</span></span> | <span data-ttu-id="84916-176">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-176">Default Value</span></span> | <span data-ttu-id="84916-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="84916-178">90 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-178">90 seconds</span></span> | <span data-ttu-id="84916-179">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="84916-180">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="84916-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="84916-181">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="84916-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="84916-182">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-182">Option</span></span> | <span data-ttu-id="84916-183">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-183">Default Value</span></span> | <span data-ttu-id="84916-184">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="84916-185">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-185">5 seconds</span></span> | <span data-ttu-id="84916-186">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="84916-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="84916-187">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="84916-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="84916-188">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="84916-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="84916-189">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-189">Configure client options</span></span>

<span data-ttu-id="84916-190">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="84916-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="84916-191">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="84916-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="84916-192">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-192">Configure logging</span></span>

<span data-ttu-id="84916-193">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="84916-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="84916-194">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="84916-195">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-196">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="84916-197">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="84916-198">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="84916-199">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="84916-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="84916-200">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="84916-201">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="84916-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="84916-202">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="84916-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="84916-203">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="84916-204">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="84916-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="84916-205">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="84916-205">The following table lists the available log levels.</span></span> <span data-ttu-id="84916-206">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="84916-207">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="84916-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="84916-208">Dize</span><span class="sxs-lookup"><span data-stu-id="84916-208">String</span></span>                      | <span data-ttu-id="84916-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="84916-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="84916-210">`info` **veya** `information`</span><span class="sxs-lookup"><span data-stu-id="84916-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="84916-211">`warn` **veya** `warning`</span><span class="sxs-lookup"><span data-stu-id="84916-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="84916-212">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="84916-213">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="84916-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="84916-214">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="84916-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="84916-215">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="84916-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="84916-216">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="84916-217">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="84916-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="84916-218">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="84916-219">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-219">Configure allowed transports</span></span>

<span data-ttu-id="84916-220">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="84916-221">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="84916-222">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="84916-222">All transports are enabled by default.</span></span>

<span data-ttu-id="84916-223">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="84916-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="84916-224">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="84916-225">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="84916-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="84916-226">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84916-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="84916-227">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="84916-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="84916-228">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="84916-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="84916-229">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-229">Configure bearer authentication</span></span>

<span data-ttu-id="84916-230">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="84916-231">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="84916-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="84916-232">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="84916-233">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="84916-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="84916-234">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="84916-235">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="84916-236">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="84916-237">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="84916-238">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="84916-239">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="84916-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="84916-240">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="84916-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-241">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-242">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-242">Option</span></span> | <span data-ttu-id="84916-243">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-243">Default value</span></span> | <span data-ttu-id="84916-244">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="84916-245">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-246">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-246">Timeout for server activity.</span></span> <span data-ttu-id="84916-247">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-248">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-249">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="84916-250">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-250">15 seconds</span></span> | <span data-ttu-id="84916-251">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-252">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-253">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-254">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-255">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-255">15 seconds</span></span> | <span data-ttu-id="84916-256">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-257">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-258">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="84916-259">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="84916-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="84916-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-261">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-261">Option</span></span> | <span data-ttu-id="84916-262">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-262">Default value</span></span> | <span data-ttu-id="84916-263">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="84916-264">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-265">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-265">Timeout for server activity.</span></span> <span data-ttu-id="84916-266">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="84916-267">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-268">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="84916-269">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-270">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-271">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-272">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-273">Java</span><span class="sxs-lookup"><span data-stu-id="84916-273">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-274">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-274">Option</span></span> | <span data-ttu-id="84916-275">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-275">Default value</span></span> | <span data-ttu-id="84916-276">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="84916-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="84916-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="84916-278">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-279">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-279">Timeout for server activity.</span></span> <span data-ttu-id="84916-280">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-281">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-282">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="84916-283">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-283">15 seconds</span></span> | <span data-ttu-id="84916-284">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-285">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-286">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-287">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="84916-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="84916-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="84916-289">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-290">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-291">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-292">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="84916-293">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-293">Configure additional options</span></span>

<span data-ttu-id="84916-294">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="84916-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-295">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-296">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-296">.NET Option</span></span> |  <span data-ttu-id="84916-297">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-297">Default value</span></span> | <span data-ttu-id="84916-298">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="84916-299">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="84916-300">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-301">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-302">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="84916-303">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-303">Empty</span></span> | <span data-ttu-id="84916-304">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="84916-305">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-305">Empty</span></span> | <span data-ttu-id="84916-306">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="84916-307">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-307">Empty</span></span> | <span data-ttu-id="84916-308">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="84916-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="84916-309">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-309">5 seconds</span></span> | <span data-ttu-id="84916-310">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84916-310">WebSockets only.</span></span> <span data-ttu-id="84916-311">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="84916-312">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="84916-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="84916-313">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-313">Empty</span></span> | <span data-ttu-id="84916-314">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="84916-315">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="84916-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="84916-316">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="84916-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="84916-317">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="84916-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="84916-318">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="84916-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="84916-319">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="84916-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="84916-320">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="84916-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="84916-321">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="84916-322">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="84916-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="84916-323">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="84916-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="84916-324">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="84916-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="84916-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-326">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-326">JavaScript Option</span></span> | <span data-ttu-id="84916-327">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-327">Default Value</span></span> | <span data-ttu-id="84916-328">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="84916-329">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="84916-330">Her HTTP isteğiyle gönderilen üstbilgilerin sözlüğü.</span><span class="sxs-lookup"><span data-stu-id="84916-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="84916-331">Üst bilgilerin tarayıcıda gönderilmesi WebSockets veya Stream için çalışmaz <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> .</span><span class="sxs-lookup"><span data-stu-id="84916-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="84916-332">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="84916-333">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-334">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-335">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="84916-336">CORS isteğiyle kimlik bilgilerinin gönderilip gönderilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="84916-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="84916-337">Azure App Service, yapışkan oturumlar için tanımlama bilgilerini kullanır ve bu seçeneğin doğru şekilde çalışmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="84916-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="84916-338">CORS hakkında daha fazla bilgi için SignalR bkz <xref:signalr/security#cross-origin-resource-sharing> ..</span><span class="sxs-lookup"><span data-stu-id="84916-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-339">Java</span><span class="sxs-lookup"><span data-stu-id="84916-339">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-340">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-340">Java Option</span></span> | <span data-ttu-id="84916-341">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-341">Default Value</span></span> | <span data-ttu-id="84916-342">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="84916-343">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="84916-344">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-345">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-346">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="84916-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="84916-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="84916-348">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-348">Empty</span></span> | <span data-ttu-id="84916-349">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="84916-350">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="84916-351">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="84916-352">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="84916-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="84916-353">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84916-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="84916-354">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="84916-355">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="84916-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="84916-356">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="84916-357">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="84916-358">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84916-359">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="84916-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="84916-360">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="84916-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="84916-361">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="84916-362">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84916-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="84916-363">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="84916-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="84916-364">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="84916-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="84916-365">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="84916-366">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="84916-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="84916-367">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="84916-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="84916-368">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-368">MessagePack serialization options</span></span>

<span data-ttu-id="84916-369">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="84916-370">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="84916-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-371">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="84916-372">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-372">Configure server options</span></span>

<span data-ttu-id="84916-373">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="84916-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="84916-374">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-374">Option</span></span> | <span data-ttu-id="84916-375">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-375">Default Value</span></span> | <span data-ttu-id="84916-376">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="84916-377">30 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-377">30 seconds</span></span> | <span data-ttu-id="84916-378">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="84916-379">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="84916-380">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="84916-381">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-381">15 seconds</span></span> | <span data-ttu-id="84916-382">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="84916-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="84916-383">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-384">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-385">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-385">15 seconds</span></span> | <span data-ttu-id="84916-386">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="84916-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="84916-387">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="84916-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="84916-388">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="84916-389">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="84916-389">All installed protocols</span></span> | <span data-ttu-id="84916-390">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="84916-390">Protocols supported by this hub.</span></span> <span data-ttu-id="84916-391">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="84916-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="84916-392">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="84916-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="84916-393">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="84916-394">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="84916-395">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="84916-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="84916-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-396">32 KB</span></span> | <span data-ttu-id="84916-397">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="84916-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="84916-398">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="84916-399">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="84916-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="84916-400">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="84916-401">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="84916-402">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="84916-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="84916-403">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="84916-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="84916-404">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-404">Option</span></span> | <span data-ttu-id="84916-405">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-405">Default Value</span></span> | <span data-ttu-id="84916-406">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="84916-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-407">32 KB</span></span> | <span data-ttu-id="84916-408">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="84916-409">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="84916-410">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="84916-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="84916-411">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="84916-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="84916-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-412">32 KB</span></span> | <span data-ttu-id="84916-413">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="84916-414">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="84916-415">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="84916-415">All Transports are enabled.</span></span> | <span data-ttu-id="84916-416">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="84916-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="84916-417">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-417">See below.</span></span> | <span data-ttu-id="84916-418">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="84916-419">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-419">See below.</span></span> | <span data-ttu-id="84916-420">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="84916-421">0</span><span class="sxs-lookup"><span data-stu-id="84916-421">0</span></span> | <span data-ttu-id="84916-422">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="84916-423">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="84916-424">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="84916-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="84916-425">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-425">Option</span></span> | <span data-ttu-id="84916-426">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-426">Default Value</span></span> | <span data-ttu-id="84916-427">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="84916-428">90 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-428">90 seconds</span></span> | <span data-ttu-id="84916-429">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="84916-430">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="84916-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="84916-431">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="84916-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="84916-432">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-432">Option</span></span> | <span data-ttu-id="84916-433">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-433">Default Value</span></span> | <span data-ttu-id="84916-434">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="84916-435">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-435">5 seconds</span></span> | <span data-ttu-id="84916-436">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="84916-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="84916-437">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="84916-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="84916-438">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="84916-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="84916-439">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-439">Configure client options</span></span>

<span data-ttu-id="84916-440">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="84916-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="84916-441">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="84916-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="84916-442">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-442">Configure logging</span></span>

<span data-ttu-id="84916-443">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="84916-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="84916-444">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="84916-445">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-446">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="84916-447">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="84916-448">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="84916-449">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="84916-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="84916-450">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="84916-451">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="84916-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="84916-452">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="84916-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="84916-453">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="84916-454">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="84916-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="84916-455">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="84916-455">The following table lists the available log levels.</span></span> <span data-ttu-id="84916-456">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="84916-457">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="84916-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="84916-458">Dize</span><span class="sxs-lookup"><span data-stu-id="84916-458">String</span></span>                      | <span data-ttu-id="84916-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="84916-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="84916-460">`info` **veya** `information`</span><span class="sxs-lookup"><span data-stu-id="84916-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="84916-461">`warn` **veya** `warning`</span><span class="sxs-lookup"><span data-stu-id="84916-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="84916-462">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="84916-463">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="84916-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="84916-464">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="84916-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="84916-465">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="84916-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="84916-466">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="84916-467">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="84916-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="84916-468">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="84916-469">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-469">Configure allowed transports</span></span>

<span data-ttu-id="84916-470">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="84916-471">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="84916-472">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="84916-472">All transports are enabled by default.</span></span>

<span data-ttu-id="84916-473">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="84916-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="84916-474">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="84916-475">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="84916-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="84916-476">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84916-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="84916-477">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="84916-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="84916-478">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="84916-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="84916-479">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-479">Configure bearer authentication</span></span>

<span data-ttu-id="84916-480">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="84916-481">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="84916-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="84916-482">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="84916-483">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="84916-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="84916-484">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="84916-485">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="84916-486">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="84916-487">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="84916-488">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="84916-489">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="84916-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="84916-490">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="84916-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-491">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-492">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-492">Option</span></span> | <span data-ttu-id="84916-493">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-493">Default value</span></span> | <span data-ttu-id="84916-494">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="84916-495">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-496">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-496">Timeout for server activity.</span></span> <span data-ttu-id="84916-497">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-498">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-499">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="84916-500">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-500">15 seconds</span></span> | <span data-ttu-id="84916-501">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-502">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-503">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-504">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-505">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-505">15 seconds</span></span> | <span data-ttu-id="84916-506">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-507">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-508">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="84916-509">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="84916-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="84916-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-511">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-511">Option</span></span> | <span data-ttu-id="84916-512">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-512">Default value</span></span> | <span data-ttu-id="84916-513">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="84916-514">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-515">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-515">Timeout for server activity.</span></span> <span data-ttu-id="84916-516">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="84916-517">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-518">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="84916-519">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-520">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-521">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-522">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-523">Java</span><span class="sxs-lookup"><span data-stu-id="84916-523">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-524">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-524">Option</span></span> | <span data-ttu-id="84916-525">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-525">Default value</span></span> | <span data-ttu-id="84916-526">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="84916-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="84916-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="84916-528">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-529">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-529">Timeout for server activity.</span></span> <span data-ttu-id="84916-530">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-531">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-532">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="84916-533">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-533">15 seconds</span></span> | <span data-ttu-id="84916-534">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-535">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-536">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-537">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="84916-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="84916-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="84916-539">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-540">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-541">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-542">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="84916-543">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-543">Configure additional options</span></span>

<span data-ttu-id="84916-544">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="84916-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-545">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-546">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-546">.NET Option</span></span> |  <span data-ttu-id="84916-547">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-547">Default value</span></span> | <span data-ttu-id="84916-548">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="84916-549">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="84916-550">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-551">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-552">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="84916-553">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-553">Empty</span></span> | <span data-ttu-id="84916-554">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="84916-555">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-555">Empty</span></span> | <span data-ttu-id="84916-556">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="84916-557">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-557">Empty</span></span> | <span data-ttu-id="84916-558">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="84916-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="84916-559">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-559">5 seconds</span></span> | <span data-ttu-id="84916-560">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84916-560">WebSockets only.</span></span> <span data-ttu-id="84916-561">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="84916-562">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="84916-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="84916-563">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-563">Empty</span></span> | <span data-ttu-id="84916-564">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="84916-565">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="84916-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="84916-566">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="84916-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="84916-567">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="84916-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="84916-568">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="84916-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="84916-569">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="84916-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="84916-570">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="84916-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="84916-571">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="84916-572">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="84916-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="84916-573">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="84916-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="84916-574">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="84916-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="84916-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-576">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-576">JavaScript Option</span></span> | <span data-ttu-id="84916-577">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-577">Default Value</span></span> | <span data-ttu-id="84916-578">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="84916-579">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="84916-580">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="84916-581">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-582">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-583">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-584">Java</span><span class="sxs-lookup"><span data-stu-id="84916-584">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-585">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-585">Java Option</span></span> | <span data-ttu-id="84916-586">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-586">Default Value</span></span> | <span data-ttu-id="84916-587">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="84916-588">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="84916-589">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-590">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-591">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="84916-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="84916-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="84916-593">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-593">Empty</span></span> | <span data-ttu-id="84916-594">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="84916-595">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="84916-596">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="84916-597">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="84916-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="84916-598">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84916-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="84916-599">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="84916-600">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="84916-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="84916-601">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="84916-602">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="84916-603">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="84916-604">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="84916-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="84916-605">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="84916-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="84916-606">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="84916-607">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84916-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="84916-608">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="84916-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="84916-609">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="84916-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="84916-610">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="84916-611">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="84916-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="84916-612">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="84916-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="84916-613">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-613">MessagePack serialization options</span></span>

<span data-ttu-id="84916-614">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="84916-615">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="84916-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-616">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="84916-617">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-617">Configure server options</span></span>

<span data-ttu-id="84916-618">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="84916-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="84916-619">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-619">Option</span></span> | <span data-ttu-id="84916-620">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-620">Default Value</span></span> | <span data-ttu-id="84916-621">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="84916-622">30 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-622">30 seconds</span></span> | <span data-ttu-id="84916-623">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="84916-624">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="84916-625">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="84916-626">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-626">15 seconds</span></span> | <span data-ttu-id="84916-627">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="84916-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="84916-628">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-629">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-630">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-630">15 seconds</span></span> | <span data-ttu-id="84916-631">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="84916-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="84916-632">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="84916-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="84916-633">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="84916-634">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="84916-634">All installed protocols</span></span> | <span data-ttu-id="84916-635">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="84916-635">Protocols supported by this hub.</span></span> <span data-ttu-id="84916-636">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="84916-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="84916-637">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="84916-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="84916-638">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="84916-639">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="84916-640">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="84916-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="84916-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-641">32 KB</span></span> | <span data-ttu-id="84916-642">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="84916-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="84916-643">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="84916-644">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="84916-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="84916-645">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="84916-646">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="84916-647">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="84916-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="84916-648">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="84916-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="84916-649">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-649">Option</span></span> | <span data-ttu-id="84916-650">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-650">Default Value</span></span> | <span data-ttu-id="84916-651">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="84916-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-652">32 KB</span></span> | <span data-ttu-id="84916-653">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="84916-654">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="84916-655">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="84916-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="84916-656">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="84916-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="84916-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-657">32 KB</span></span> | <span data-ttu-id="84916-658">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="84916-659">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="84916-660">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="84916-660">All Transports are enabled.</span></span> | <span data-ttu-id="84916-661">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="84916-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="84916-662">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-662">See below.</span></span> | <span data-ttu-id="84916-663">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="84916-664">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-664">See below.</span></span> | <span data-ttu-id="84916-665">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="84916-666">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="84916-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="84916-667">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-667">Option</span></span> | <span data-ttu-id="84916-668">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-668">Default Value</span></span> | <span data-ttu-id="84916-669">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="84916-670">90 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-670">90 seconds</span></span> | <span data-ttu-id="84916-671">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="84916-672">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="84916-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="84916-673">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="84916-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="84916-674">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-674">Option</span></span> | <span data-ttu-id="84916-675">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-675">Default Value</span></span> | <span data-ttu-id="84916-676">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="84916-677">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-677">5 seconds</span></span> | <span data-ttu-id="84916-678">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="84916-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="84916-679">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="84916-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="84916-680">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="84916-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="84916-681">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-681">Configure client options</span></span>

<span data-ttu-id="84916-682">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="84916-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="84916-683">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="84916-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="84916-684">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-684">Configure logging</span></span>

<span data-ttu-id="84916-685">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="84916-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="84916-686">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="84916-687">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-688">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="84916-689">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="84916-690">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="84916-691">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="84916-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="84916-692">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="84916-693">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="84916-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="84916-694">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="84916-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="84916-695">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="84916-696">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="84916-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="84916-697">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="84916-697">The following table lists the available log levels.</span></span> <span data-ttu-id="84916-698">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="84916-699">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="84916-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="84916-700">Dize</span><span class="sxs-lookup"><span data-stu-id="84916-700">String</span></span>                      | <span data-ttu-id="84916-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="84916-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="84916-702">`info` **veya** `information`</span><span class="sxs-lookup"><span data-stu-id="84916-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="84916-703">`warn` **veya** `warning`</span><span class="sxs-lookup"><span data-stu-id="84916-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="84916-704">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="84916-705">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="84916-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="84916-706">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="84916-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="84916-707">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="84916-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="84916-708">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="84916-709">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="84916-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="84916-710">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="84916-711">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-711">Configure allowed transports</span></span>

<span data-ttu-id="84916-712">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="84916-713">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="84916-714">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="84916-714">All transports are enabled by default.</span></span>

<span data-ttu-id="84916-715">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="84916-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="84916-716">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="84916-717">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="84916-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="84916-718">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="84916-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="84916-719">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="84916-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="84916-720">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="84916-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="84916-721">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-721">Configure bearer authentication</span></span>

<span data-ttu-id="84916-722">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="84916-723">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="84916-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="84916-724">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="84916-725">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="84916-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="84916-726">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="84916-727">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="84916-728">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="84916-729">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="84916-730">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="84916-731">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="84916-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="84916-732">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="84916-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-733">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-734">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-734">Option</span></span> | <span data-ttu-id="84916-735">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-735">Default value</span></span> | <span data-ttu-id="84916-736">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="84916-737">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-738">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-738">Timeout for server activity.</span></span> <span data-ttu-id="84916-739">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-740">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-741">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="84916-742">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-742">15 seconds</span></span> | <span data-ttu-id="84916-743">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-744">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-745">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-746">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-747">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-747">15 seconds</span></span> | <span data-ttu-id="84916-748">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-749">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-750">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="84916-751">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="84916-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="84916-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-753">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-753">Option</span></span> | <span data-ttu-id="84916-754">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-754">Default value</span></span> | <span data-ttu-id="84916-755">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="84916-756">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-757">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-757">Timeout for server activity.</span></span> <span data-ttu-id="84916-758">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="84916-759">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-760">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="84916-761">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-762">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-763">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-764">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-765">Java</span><span class="sxs-lookup"><span data-stu-id="84916-765">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-766">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-766">Option</span></span> | <span data-ttu-id="84916-767">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-767">Default value</span></span> | <span data-ttu-id="84916-768">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="84916-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="84916-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="84916-770">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-771">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-771">Timeout for server activity.</span></span> <span data-ttu-id="84916-772">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-773">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-774">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="84916-775">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-775">15 seconds</span></span> | <span data-ttu-id="84916-776">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-777">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-778">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-779">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="84916-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="84916-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="84916-781">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-782">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-783">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-784">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="84916-785">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-785">Configure additional options</span></span>

<span data-ttu-id="84916-786">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="84916-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-787">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-788">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-788">.NET Option</span></span> |  <span data-ttu-id="84916-789">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-789">Default value</span></span> | <span data-ttu-id="84916-790">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="84916-791">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="84916-792">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-793">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-794">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="84916-795">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-795">Empty</span></span> | <span data-ttu-id="84916-796">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="84916-797">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-797">Empty</span></span> | <span data-ttu-id="84916-798">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="84916-799">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-799">Empty</span></span> | <span data-ttu-id="84916-800">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="84916-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="84916-801">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-801">5 seconds</span></span> | <span data-ttu-id="84916-802">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84916-802">WebSockets only.</span></span> <span data-ttu-id="84916-803">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="84916-804">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="84916-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="84916-805">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-805">Empty</span></span> | <span data-ttu-id="84916-806">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="84916-807">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="84916-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="84916-808">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="84916-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="84916-809">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="84916-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="84916-810">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="84916-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="84916-811">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="84916-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="84916-812">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="84916-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="84916-813">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="84916-814">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="84916-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="84916-815">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="84916-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="84916-816">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="84916-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="84916-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-818">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-818">JavaScript Option</span></span> | <span data-ttu-id="84916-819">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-819">Default Value</span></span> | <span data-ttu-id="84916-820">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="84916-821">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="84916-822">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="84916-823">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-824">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-825">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-826">Java</span><span class="sxs-lookup"><span data-stu-id="84916-826">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-827">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-827">Java Option</span></span> | <span data-ttu-id="84916-828">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-828">Default Value</span></span> | <span data-ttu-id="84916-829">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="84916-830">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="84916-831">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-832">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-833">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="84916-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="84916-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="84916-835">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-835">Empty</span></span> | <span data-ttu-id="84916-836">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="84916-837">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="84916-838">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="84916-839">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="84916-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="84916-840">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84916-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="84916-841">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="84916-842">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="84916-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="84916-843">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="84916-844">JSON serileştirme, yöntekinizdeki [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="84916-845">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="84916-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="84916-846">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="84916-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="84916-847">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="84916-848">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84916-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="84916-849">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="84916-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="84916-850">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="84916-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="84916-851">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="84916-852">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-852">MessagePack serialization options</span></span>

<span data-ttu-id="84916-853">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="84916-854">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="84916-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-855">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="84916-856">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-856">Configure server options</span></span>

<span data-ttu-id="84916-857">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="84916-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="84916-858">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-858">Option</span></span> | <span data-ttu-id="84916-859">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-859">Default Value</span></span> | <span data-ttu-id="84916-860">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="84916-861">30 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-861">30 seconds</span></span> | <span data-ttu-id="84916-862">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="84916-863">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="84916-864">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="84916-865">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-865">15 seconds</span></span> | <span data-ttu-id="84916-866">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="84916-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="84916-867">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-868">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-869">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-869">15 seconds</span></span> | <span data-ttu-id="84916-870">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="84916-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="84916-871">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="84916-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="84916-872">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="84916-873">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="84916-873">All installed protocols</span></span> | <span data-ttu-id="84916-874">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="84916-874">Protocols supported by this hub.</span></span> <span data-ttu-id="84916-875">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="84916-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="84916-876">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="84916-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="84916-877">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="84916-878">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="84916-879">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="84916-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="84916-880">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="84916-881">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="84916-882">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="84916-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="84916-883">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="84916-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="84916-884">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-884">Option</span></span> | <span data-ttu-id="84916-885">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-885">Default Value</span></span> | <span data-ttu-id="84916-886">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="84916-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-887">32 KB</span></span> | <span data-ttu-id="84916-888">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="84916-889">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="84916-890">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="84916-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="84916-891">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="84916-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="84916-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-892">32 KB</span></span> | <span data-ttu-id="84916-893">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="84916-894">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="84916-895">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="84916-895">All Transports are enabled.</span></span> | <span data-ttu-id="84916-896">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="84916-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="84916-897">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-897">See below.</span></span> | <span data-ttu-id="84916-898">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="84916-899">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-899">See below.</span></span> | <span data-ttu-id="84916-900">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="84916-901">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="84916-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="84916-902">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-902">Option</span></span> | <span data-ttu-id="84916-903">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-903">Default Value</span></span> | <span data-ttu-id="84916-904">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="84916-905">90 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-905">90 seconds</span></span> | <span data-ttu-id="84916-906">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="84916-907">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="84916-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="84916-908">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="84916-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="84916-909">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-909">Option</span></span> | <span data-ttu-id="84916-910">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-910">Default Value</span></span> | <span data-ttu-id="84916-911">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="84916-912">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-912">5 seconds</span></span> | <span data-ttu-id="84916-913">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="84916-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="84916-914">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="84916-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="84916-915">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="84916-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="84916-916">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-916">Configure client options</span></span>

<span data-ttu-id="84916-917">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="84916-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="84916-918">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="84916-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="84916-919">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-919">Configure logging</span></span>

<span data-ttu-id="84916-920">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="84916-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="84916-921">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="84916-922">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-923">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="84916-924">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="84916-925">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="84916-926">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="84916-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="84916-927">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="84916-928">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="84916-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="84916-929">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="84916-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="84916-930">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="84916-931">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="84916-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="84916-932">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="84916-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="84916-933">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="84916-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="84916-934">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="84916-935">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="84916-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="84916-936">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="84916-937">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-937">Configure allowed transports</span></span>

<span data-ttu-id="84916-938">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="84916-939">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="84916-940">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="84916-940">All transports are enabled by default.</span></span>

<span data-ttu-id="84916-941">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="84916-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="84916-942">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="84916-943">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="84916-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="84916-944">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-944">Configure bearer authentication</span></span>

<span data-ttu-id="84916-945">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="84916-946">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="84916-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="84916-947">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="84916-948">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="84916-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="84916-949">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="84916-950">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="84916-951">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="84916-952">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="84916-953">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="84916-954">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="84916-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="84916-955">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="84916-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-956">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-957">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-957">Option</span></span> | <span data-ttu-id="84916-958">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-958">Default value</span></span> | <span data-ttu-id="84916-959">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="84916-960">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-961">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-961">Timeout for server activity.</span></span> <span data-ttu-id="84916-962">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-963">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-964">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="84916-965">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-965">15 seconds</span></span> | <span data-ttu-id="84916-966">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-967">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-968">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-969">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-970">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-970">15 seconds</span></span> | <span data-ttu-id="84916-971">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-972">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-973">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="84916-974">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="84916-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="84916-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-976">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-976">Option</span></span> | <span data-ttu-id="84916-977">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-977">Default value</span></span> | <span data-ttu-id="84916-978">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="84916-979">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-980">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-980">Timeout for server activity.</span></span> <span data-ttu-id="84916-981">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="84916-982">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-983">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="84916-984">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-985">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-986">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-987">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-988">Java</span><span class="sxs-lookup"><span data-stu-id="84916-988">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-989">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-989">Option</span></span> | <span data-ttu-id="84916-990">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-990">Default value</span></span> | <span data-ttu-id="84916-991">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="84916-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="84916-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="84916-993">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-994">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-994">Timeout for server activity.</span></span> <span data-ttu-id="84916-995">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-996">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-997">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="84916-998">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-998">15 seconds</span></span> | <span data-ttu-id="84916-999">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-1000">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-1001">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-1002">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="84916-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="84916-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="84916-1004">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="84916-1005">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="84916-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="84916-1006">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="84916-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="84916-1007">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="84916-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="84916-1008">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1008">Configure additional options</span></span>

<span data-ttu-id="84916-1009">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="84916-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-1011">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1011">.NET Option</span></span> |  <span data-ttu-id="84916-1012">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1012">Default value</span></span> | <span data-ttu-id="84916-1013">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="84916-1014">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="84916-1015">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1016">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1017">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="84916-1018">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1018">Empty</span></span> | <span data-ttu-id="84916-1019">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="84916-1020">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1020">Empty</span></span> | <span data-ttu-id="84916-1021">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="84916-1022">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1022">Empty</span></span> | <span data-ttu-id="84916-1023">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="84916-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="84916-1024">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1024">5 seconds</span></span> | <span data-ttu-id="84916-1025">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84916-1025">WebSockets only.</span></span> <span data-ttu-id="84916-1026">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="84916-1027">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="84916-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="84916-1028">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1028">Empty</span></span> | <span data-ttu-id="84916-1029">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="84916-1030">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="84916-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="84916-1031">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="84916-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="84916-1032">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="84916-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="84916-1033">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="84916-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="84916-1034">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="84916-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="84916-1035">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="84916-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="84916-1036">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="84916-1037">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="84916-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="84916-1038">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="84916-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="84916-1039">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="84916-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="84916-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-1041">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1041">JavaScript Option</span></span> | <span data-ttu-id="84916-1042">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1042">Default Value</span></span> | <span data-ttu-id="84916-1043">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="84916-1044">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="84916-1045">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="84916-1046">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1047">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1048">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-1049">Java</span><span class="sxs-lookup"><span data-stu-id="84916-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-1050">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1050">Java Option</span></span> | <span data-ttu-id="84916-1051">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1051">Default Value</span></span> | <span data-ttu-id="84916-1052">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="84916-1053">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="84916-1054">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1055">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1056">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="84916-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="84916-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="84916-1058">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1058">Empty</span></span> | <span data-ttu-id="84916-1059">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="84916-1060">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="84916-1061">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="84916-1062">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="84916-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="84916-1063">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84916-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="84916-1064">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="84916-1065">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="84916-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="84916-1066">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="84916-1067">JSON serileştirme, yöntekinizdeki [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="84916-1068">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="84916-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="84916-1069">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="84916-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="84916-1070">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="84916-1071">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="84916-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="84916-1072">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="84916-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="84916-1073">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="84916-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="84916-1074">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="84916-1075">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-1075">MessagePack serialization options</span></span>

<span data-ttu-id="84916-1076">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="84916-1077">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="84916-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-1078">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="84916-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="84916-1079">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1079">Configure server options</span></span>

<span data-ttu-id="84916-1080">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="84916-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="84916-1081">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1081">Option</span></span> | <span data-ttu-id="84916-1082">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1082">Default Value</span></span> | <span data-ttu-id="84916-1083">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="84916-1084">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1084">15 seconds</span></span> | <span data-ttu-id="84916-1085">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="84916-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="84916-1086">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-1087">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="84916-1088">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1088">15 seconds</span></span> | <span data-ttu-id="84916-1089">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="84916-1090">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="84916-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="84916-1091">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="84916-1092">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="84916-1092">All installed protocols</span></span> | <span data-ttu-id="84916-1093">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="84916-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="84916-1094">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="84916-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="84916-1095">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="84916-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="84916-1096">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="84916-1097">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="84916-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="84916-1098">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="84916-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="84916-1099">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="84916-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="84916-1100">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="84916-1101">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="84916-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="84916-1102">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="84916-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="84916-1103">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1103">Option</span></span> | <span data-ttu-id="84916-1104">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1104">Default Value</span></span> | <span data-ttu-id="84916-1105">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="84916-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-1106">32 KB</span></span> | <span data-ttu-id="84916-1107">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="84916-1108">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="84916-1109">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="84916-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="84916-1110">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="84916-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="84916-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="84916-1111">32 KB</span></span> | <span data-ttu-id="84916-1112">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="84916-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="84916-1113">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="84916-1114">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="84916-1114">All Transports are enabled.</span></span> | <span data-ttu-id="84916-1115">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="84916-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="84916-1116">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1116">See below.</span></span> | <span data-ttu-id="84916-1117">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="84916-1118">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1118">See below.</span></span> | <span data-ttu-id="84916-1119">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="84916-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="84916-1120">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="84916-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="84916-1121">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1121">Option</span></span> | <span data-ttu-id="84916-1122">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1122">Default Value</span></span> | <span data-ttu-id="84916-1123">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="84916-1124">90 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1124">90 seconds</span></span> | <span data-ttu-id="84916-1125">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="84916-1126">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="84916-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="84916-1127">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="84916-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="84916-1128">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1128">Option</span></span> | <span data-ttu-id="84916-1129">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1129">Default Value</span></span> | <span data-ttu-id="84916-1130">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="84916-1131">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1131">5 seconds</span></span> | <span data-ttu-id="84916-1132">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="84916-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="84916-1133">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="84916-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="84916-1134">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="84916-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="84916-1135">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1135">Configure client options</span></span>

<span data-ttu-id="84916-1136">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="84916-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="84916-1137">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="84916-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="84916-1138">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1138">Configure logging</span></span>

<span data-ttu-id="84916-1139">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="84916-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="84916-1140">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="84916-1141">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="84916-1142">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="84916-1143">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="84916-1144">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="84916-1145">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="84916-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="84916-1146">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="84916-1147">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="84916-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="84916-1148">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="84916-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="84916-1149">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="84916-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="84916-1150">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="84916-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="84916-1151">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="84916-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="84916-1152">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="84916-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="84916-1153">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="84916-1154">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="84916-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="84916-1155">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="84916-1156">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1156">Configure allowed transports</span></span>

<span data-ttu-id="84916-1157">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="84916-1158">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84916-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="84916-1159">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="84916-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="84916-1160">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="84916-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="84916-1161">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="84916-1162">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1162">Configure bearer authentication</span></span>

<span data-ttu-id="84916-1163">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="84916-1164">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="84916-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="84916-1165">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84916-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="84916-1166">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="84916-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="84916-1167">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="84916-1168">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="84916-1169">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="84916-1170">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="84916-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="84916-1171">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84916-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="84916-1172">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="84916-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="84916-1173">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="84916-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-1175">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1175">Option</span></span> | <span data-ttu-id="84916-1176">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1176">Default value</span></span> | <span data-ttu-id="84916-1177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="84916-1178">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-1179">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-1179">Timeout for server activity.</span></span> <span data-ttu-id="84916-1180">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-1181">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-1182">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="84916-1183">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1183">15 seconds</span></span> | <span data-ttu-id="84916-1184">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-1185">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="84916-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="84916-1186">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-1187">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="84916-1188">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="84916-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="84916-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-1190">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1190">Option</span></span> | <span data-ttu-id="84916-1191">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1191">Default value</span></span> | <span data-ttu-id="84916-1192">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="84916-1193">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-1194">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-1194">Timeout for server activity.</span></span> <span data-ttu-id="84916-1195">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="84916-1196">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-1197">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-1198">Java</span><span class="sxs-lookup"><span data-stu-id="84916-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-1199">Seçenek</span><span class="sxs-lookup"><span data-stu-id="84916-1199">Option</span></span> | <span data-ttu-id="84916-1200">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1200">Default value</span></span> | <span data-ttu-id="84916-1201">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="84916-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="84916-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="84916-1203">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="84916-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="84916-1204">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-1204">Timeout for server activity.</span></span> <span data-ttu-id="84916-1205">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-1206">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84916-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="84916-1207">Önerilen değer, ping bir sürenin gelmesi için en az iki değerin değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="84916-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="84916-1208">15 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1208">15 seconds</span></span> | <span data-ttu-id="84916-1209">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="84916-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="84916-1210">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="84916-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="84916-1211">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="84916-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="84916-1212">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="84916-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="84916-1213">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="84916-1213">Configure additional options</span></span>

<span data-ttu-id="84916-1214">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="84916-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="84916-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="84916-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="84916-1216">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1216">.NET Option</span></span> |  <span data-ttu-id="84916-1217">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1217">Default value</span></span> | <span data-ttu-id="84916-1218">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="84916-1219">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="84916-1220">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1221">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1222">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="84916-1223">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1223">Empty</span></span> | <span data-ttu-id="84916-1224">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="84916-1225">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1225">Empty</span></span> | <span data-ttu-id="84916-1226">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="84916-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="84916-1227">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1227">Empty</span></span> | <span data-ttu-id="84916-1228">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="84916-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="84916-1229">5 saniye</span><span class="sxs-lookup"><span data-stu-id="84916-1229">5 seconds</span></span> | <span data-ttu-id="84916-1230">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84916-1230">WebSockets only.</span></span> <span data-ttu-id="84916-1231">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="84916-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="84916-1232">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="84916-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="84916-1233">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1233">Empty</span></span> | <span data-ttu-id="84916-1234">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="84916-1235">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="84916-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="84916-1236">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="84916-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="84916-1237">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="84916-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="84916-1238">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="84916-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="84916-1239">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="84916-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="84916-1240">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="84916-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="84916-1241">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="84916-1242">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="84916-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="84916-1243">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="84916-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="84916-1244">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="84916-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="84916-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="84916-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="84916-1246">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1246">JavaScript Option</span></span> | <span data-ttu-id="84916-1247">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1247">Default Value</span></span> | <span data-ttu-id="84916-1248">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="84916-1249">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="84916-1250">`true`İstemci tarafından gönderilen ve alınan iletilerin bayt/karakter sayısını günlüğe kaydetmek için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="84916-1251">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1252">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1253">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="84916-1254">Java</span><span class="sxs-lookup"><span data-stu-id="84916-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="84916-1255">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="84916-1255">Java Option</span></span> | <span data-ttu-id="84916-1256">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="84916-1256">Default Value</span></span> | <span data-ttu-id="84916-1257">Açıklama</span><span class="sxs-lookup"><span data-stu-id="84916-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="84916-1258">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="84916-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="84916-1259">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84916-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="84916-1260">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="84916-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="84916-1261">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="84916-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="84916-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="84916-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="84916-1263">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="84916-1263">Empty</span></span> | <span data-ttu-id="84916-1264">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="84916-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="84916-1265">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="84916-1266">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="84916-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="84916-1267">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="84916-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="84916-1268">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84916-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
