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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 09866f1fd56a4d0747ef3814c85ab5070cfb8d59
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756125"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="50204-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="50204-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="50204-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="50204-105">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="50204-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="50204-106">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="50204-107">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="50204-108">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50204-109">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="50204-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="50204-110">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="50204-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="50204-111">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="50204-112">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50204-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="50204-113">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="50204-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="50204-114">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="50204-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="50204-115">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="50204-116">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="50204-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="50204-117">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="50204-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="50204-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-118">MessagePack serialization options</span></span>

<span data-ttu-id="50204-119">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="50204-120">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="50204-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-121">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="50204-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-122">Configure server options</span></span>

<span data-ttu-id="50204-123">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="50204-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="50204-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-124">Option</span></span> | <span data-ttu-id="50204-125">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-125">Default Value</span></span> | <span data-ttu-id="50204-126">Description</span><span class="sxs-lookup"><span data-stu-id="50204-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="50204-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-127">30 seconds</span></span> | <span data-ttu-id="50204-128">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="50204-129">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="50204-130">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="50204-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-131">15 seconds</span></span> | <span data-ttu-id="50204-132">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="50204-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="50204-133">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-134">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-135">15 seconds</span></span> | <span data-ttu-id="50204-136">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="50204-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="50204-137">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="50204-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="50204-138">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="50204-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="50204-139">All installed protocols</span></span> | <span data-ttu-id="50204-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="50204-140">Protocols supported by this hub.</span></span> <span data-ttu-id="50204-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="50204-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="50204-142">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="50204-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="50204-143">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="50204-144">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="50204-145">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="50204-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="50204-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-146">32 KB</span></span> | <span data-ttu-id="50204-147">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="50204-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="50204-148">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="50204-149">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="50204-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="50204-150">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="50204-151">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="50204-152">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="50204-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="50204-153">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="50204-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="50204-154">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-154">Option</span></span> | <span data-ttu-id="50204-155">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-155">Default Value</span></span> | <span data-ttu-id="50204-156">Description</span><span class="sxs-lookup"><span data-stu-id="50204-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="50204-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-157">32 KB</span></span> | <span data-ttu-id="50204-158">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="50204-159">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="50204-160">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="50204-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="50204-161">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="50204-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="50204-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-162">32 KB</span></span> | <span data-ttu-id="50204-163">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="50204-164">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="50204-165">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="50204-165">All Transports are enabled.</span></span> | <span data-ttu-id="50204-166">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="50204-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="50204-167">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-167">See below.</span></span> | <span data-ttu-id="50204-168">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="50204-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-169">See below.</span></span> | <span data-ttu-id="50204-170">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="50204-171">0</span><span class="sxs-lookup"><span data-stu-id="50204-171">0</span></span> | <span data-ttu-id="50204-172">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="50204-173">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="50204-174">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="50204-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="50204-175">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-175">Option</span></span> | <span data-ttu-id="50204-176">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-176">Default Value</span></span> | <span data-ttu-id="50204-177">Description</span><span class="sxs-lookup"><span data-stu-id="50204-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="50204-178">90 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-178">90 seconds</span></span> | <span data-ttu-id="50204-179">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="50204-180">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="50204-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="50204-181">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="50204-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="50204-182">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-182">Option</span></span> | <span data-ttu-id="50204-183">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-183">Default Value</span></span> | <span data-ttu-id="50204-184">Description</span><span class="sxs-lookup"><span data-stu-id="50204-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="50204-185">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-185">5 seconds</span></span> | <span data-ttu-id="50204-186">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="50204-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="50204-187">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="50204-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="50204-188">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="50204-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="50204-189">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-189">Configure client options</span></span>

<span data-ttu-id="50204-190">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="50204-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="50204-191">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="50204-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="50204-192">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-192">Configure logging</span></span>

<span data-ttu-id="50204-193">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="50204-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="50204-194">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="50204-195">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-196">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="50204-197">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="50204-198">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="50204-199">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="50204-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="50204-200">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="50204-201">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="50204-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="50204-202">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="50204-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="50204-203">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="50204-204">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="50204-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="50204-205">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="50204-205">The following table lists the available log levels.</span></span> <span data-ttu-id="50204-206">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="50204-207">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="50204-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="50204-208">Dize</span><span class="sxs-lookup"><span data-stu-id="50204-208">String</span></span>                      | <span data-ttu-id="50204-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="50204-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="50204-210">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="50204-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="50204-211">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="50204-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="50204-212">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="50204-213">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="50204-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="50204-214">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50204-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="50204-215">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="50204-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="50204-216">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="50204-217">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="50204-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="50204-218">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="50204-219">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-219">Configure allowed transports</span></span>

<span data-ttu-id="50204-220">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="50204-221">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="50204-222">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="50204-222">All transports are enabled by default.</span></span>

<span data-ttu-id="50204-223">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="50204-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="50204-224">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="50204-225">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="50204-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="50204-226">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="50204-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="50204-227">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="50204-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="50204-228">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="50204-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="50204-229">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-229">Configure bearer authentication</span></span>

<span data-ttu-id="50204-230">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="50204-231">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="50204-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="50204-232">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="50204-233">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="50204-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="50204-234">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="50204-235">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="50204-236">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="50204-237">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="50204-238">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="50204-239">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="50204-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="50204-240">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="50204-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-241">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-242">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-242">Option</span></span> | <span data-ttu-id="50204-243">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-243">Default value</span></span> | <span data-ttu-id="50204-244">Description</span><span class="sxs-lookup"><span data-stu-id="50204-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="50204-245">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-246">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-246">Timeout for server activity.</span></span> <span data-ttu-id="50204-247">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-248">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-249">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="50204-250">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-250">15 seconds</span></span> | <span data-ttu-id="50204-251">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-252">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-253">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-254">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-255">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-255">15 seconds</span></span> | <span data-ttu-id="50204-256">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-257">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-258">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="50204-259">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="50204-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="50204-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-261">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-261">Option</span></span> | <span data-ttu-id="50204-262">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-262">Default value</span></span> | <span data-ttu-id="50204-263">Description</span><span class="sxs-lookup"><span data-stu-id="50204-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="50204-264">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-265">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-265">Timeout for server activity.</span></span> <span data-ttu-id="50204-266">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="50204-267">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-268">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="50204-269">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-270">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-271">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-272">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-273">Java</span><span class="sxs-lookup"><span data-stu-id="50204-273">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-274">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-274">Option</span></span> | <span data-ttu-id="50204-275">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-275">Default value</span></span> | <span data-ttu-id="50204-276">Description</span><span class="sxs-lookup"><span data-stu-id="50204-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="50204-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="50204-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="50204-278">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-279">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-279">Timeout for server activity.</span></span> <span data-ttu-id="50204-280">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-281">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-282">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="50204-283">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-283">15 seconds</span></span> | <span data-ttu-id="50204-284">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-285">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-286">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-287">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="50204-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="50204-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="50204-289">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-290">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-291">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-292">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="50204-293">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-293">Configure additional options</span></span>

<span data-ttu-id="50204-294">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="50204-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-295">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-296">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-296">.NET Option</span></span> |  <span data-ttu-id="50204-297">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-297">Default value</span></span> | <span data-ttu-id="50204-298">Description</span><span class="sxs-lookup"><span data-stu-id="50204-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="50204-299">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="50204-300">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-301">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-302">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="50204-303">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-303">Empty</span></span> | <span data-ttu-id="50204-304">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="50204-305">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-305">Empty</span></span> | <span data-ttu-id="50204-306">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="50204-307">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-307">Empty</span></span> | <span data-ttu-id="50204-308">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="50204-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="50204-309">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-309">5 seconds</span></span> | <span data-ttu-id="50204-310">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="50204-310">WebSockets only.</span></span> <span data-ttu-id="50204-311">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="50204-312">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="50204-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="50204-313">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-313">Empty</span></span> | <span data-ttu-id="50204-314">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="50204-315">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="50204-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="50204-316">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="50204-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="50204-317">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="50204-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="50204-318">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="50204-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="50204-319">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="50204-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="50204-320">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="50204-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="50204-321">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="50204-322">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="50204-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="50204-323">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="50204-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="50204-324">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="50204-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="50204-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-326">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-326">JavaScript Option</span></span> | <span data-ttu-id="50204-327">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-327">Default Value</span></span> | <span data-ttu-id="50204-328">Description</span><span class="sxs-lookup"><span data-stu-id="50204-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="50204-329">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="50204-330">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-331">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-332">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="50204-333">CORS isteğiyle kimlik bilgilerinin gönderilip gönderilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="50204-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="50204-334">Azure App Service, yapışkan oturumlar için tanımlama bilgilerini kullanır ve bu seçeneğin doğru şekilde çalışmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="50204-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="50204-335">CORS hakkında daha fazla bilgi için SignalR bkz <xref:signalr/security#cross-origin-resource-sharing> ..</span><span class="sxs-lookup"><span data-stu-id="50204-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-336">Java</span><span class="sxs-lookup"><span data-stu-id="50204-336">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-337">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-337">Java Option</span></span> | <span data-ttu-id="50204-338">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-338">Default Value</span></span> | <span data-ttu-id="50204-339">Description</span><span class="sxs-lookup"><span data-stu-id="50204-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="50204-340">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="50204-341">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-342">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-343">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="50204-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="50204-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="50204-345">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-345">Empty</span></span> | <span data-ttu-id="50204-346">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="50204-347">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="50204-348">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="50204-349">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="50204-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="50204-350">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50204-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="50204-351">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="50204-352">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="50204-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="50204-353">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="50204-354">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="50204-355">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50204-356">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="50204-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="50204-357">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="50204-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="50204-358">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="50204-359">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50204-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="50204-360">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="50204-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="50204-361">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="50204-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="50204-362">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="50204-363">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="50204-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="50204-364">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="50204-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="50204-365">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-365">MessagePack serialization options</span></span>

<span data-ttu-id="50204-366">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="50204-367">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="50204-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-368">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="50204-369">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-369">Configure server options</span></span>

<span data-ttu-id="50204-370">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="50204-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="50204-371">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-371">Option</span></span> | <span data-ttu-id="50204-372">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-372">Default Value</span></span> | <span data-ttu-id="50204-373">Description</span><span class="sxs-lookup"><span data-stu-id="50204-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="50204-374">30 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-374">30 seconds</span></span> | <span data-ttu-id="50204-375">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="50204-376">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="50204-377">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="50204-378">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-378">15 seconds</span></span> | <span data-ttu-id="50204-379">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="50204-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="50204-380">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-381">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-382">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-382">15 seconds</span></span> | <span data-ttu-id="50204-383">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="50204-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="50204-384">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="50204-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="50204-385">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="50204-386">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="50204-386">All installed protocols</span></span> | <span data-ttu-id="50204-387">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="50204-387">Protocols supported by this hub.</span></span> <span data-ttu-id="50204-388">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="50204-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="50204-389">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="50204-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="50204-390">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="50204-391">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="50204-392">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="50204-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="50204-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-393">32 KB</span></span> | <span data-ttu-id="50204-394">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="50204-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="50204-395">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="50204-396">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="50204-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="50204-397">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="50204-398">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="50204-399">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="50204-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="50204-400">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="50204-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="50204-401">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-401">Option</span></span> | <span data-ttu-id="50204-402">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-402">Default Value</span></span> | <span data-ttu-id="50204-403">Description</span><span class="sxs-lookup"><span data-stu-id="50204-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="50204-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-404">32 KB</span></span> | <span data-ttu-id="50204-405">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="50204-406">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="50204-407">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="50204-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="50204-408">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="50204-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="50204-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-409">32 KB</span></span> | <span data-ttu-id="50204-410">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="50204-411">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="50204-412">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="50204-412">All Transports are enabled.</span></span> | <span data-ttu-id="50204-413">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="50204-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="50204-414">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-414">See below.</span></span> | <span data-ttu-id="50204-415">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="50204-416">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-416">See below.</span></span> | <span data-ttu-id="50204-417">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="50204-418">0</span><span class="sxs-lookup"><span data-stu-id="50204-418">0</span></span> | <span data-ttu-id="50204-419">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="50204-420">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="50204-421">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="50204-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="50204-422">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-422">Option</span></span> | <span data-ttu-id="50204-423">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-423">Default Value</span></span> | <span data-ttu-id="50204-424">Description</span><span class="sxs-lookup"><span data-stu-id="50204-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="50204-425">90 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-425">90 seconds</span></span> | <span data-ttu-id="50204-426">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="50204-427">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="50204-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="50204-428">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="50204-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="50204-429">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-429">Option</span></span> | <span data-ttu-id="50204-430">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-430">Default Value</span></span> | <span data-ttu-id="50204-431">Description</span><span class="sxs-lookup"><span data-stu-id="50204-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="50204-432">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-432">5 seconds</span></span> | <span data-ttu-id="50204-433">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="50204-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="50204-434">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="50204-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="50204-435">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="50204-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="50204-436">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-436">Configure client options</span></span>

<span data-ttu-id="50204-437">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="50204-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="50204-438">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="50204-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="50204-439">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-439">Configure logging</span></span>

<span data-ttu-id="50204-440">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="50204-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="50204-441">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="50204-442">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-443">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="50204-444">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="50204-445">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="50204-446">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="50204-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="50204-447">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="50204-448">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="50204-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="50204-449">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="50204-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="50204-450">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="50204-451">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="50204-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="50204-452">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="50204-452">The following table lists the available log levels.</span></span> <span data-ttu-id="50204-453">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="50204-454">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="50204-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="50204-455">Dize</span><span class="sxs-lookup"><span data-stu-id="50204-455">String</span></span>                      | <span data-ttu-id="50204-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="50204-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="50204-457">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="50204-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="50204-458">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="50204-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="50204-459">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="50204-460">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="50204-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="50204-461">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50204-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="50204-462">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="50204-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="50204-463">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="50204-464">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="50204-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="50204-465">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="50204-466">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-466">Configure allowed transports</span></span>

<span data-ttu-id="50204-467">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="50204-468">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="50204-469">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="50204-469">All transports are enabled by default.</span></span>

<span data-ttu-id="50204-470">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="50204-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="50204-471">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="50204-472">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="50204-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="50204-473">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="50204-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="50204-474">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="50204-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="50204-475">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="50204-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="50204-476">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-476">Configure bearer authentication</span></span>

<span data-ttu-id="50204-477">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="50204-478">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="50204-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="50204-479">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="50204-480">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="50204-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="50204-481">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="50204-482">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="50204-483">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="50204-484">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="50204-485">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="50204-486">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="50204-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="50204-487">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="50204-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-488">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-489">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-489">Option</span></span> | <span data-ttu-id="50204-490">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-490">Default value</span></span> | <span data-ttu-id="50204-491">Description</span><span class="sxs-lookup"><span data-stu-id="50204-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="50204-492">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-493">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-493">Timeout for server activity.</span></span> <span data-ttu-id="50204-494">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-495">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-496">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="50204-497">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-497">15 seconds</span></span> | <span data-ttu-id="50204-498">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-499">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-500">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-501">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-502">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-502">15 seconds</span></span> | <span data-ttu-id="50204-503">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-504">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-505">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="50204-506">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="50204-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="50204-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-508">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-508">Option</span></span> | <span data-ttu-id="50204-509">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-509">Default value</span></span> | <span data-ttu-id="50204-510">Description</span><span class="sxs-lookup"><span data-stu-id="50204-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="50204-511">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-512">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-512">Timeout for server activity.</span></span> <span data-ttu-id="50204-513">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="50204-514">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-515">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="50204-516">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-517">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-518">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-519">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-520">Java</span><span class="sxs-lookup"><span data-stu-id="50204-520">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-521">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-521">Option</span></span> | <span data-ttu-id="50204-522">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-522">Default value</span></span> | <span data-ttu-id="50204-523">Description</span><span class="sxs-lookup"><span data-stu-id="50204-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="50204-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="50204-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="50204-525">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-526">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-526">Timeout for server activity.</span></span> <span data-ttu-id="50204-527">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-528">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-529">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="50204-530">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-530">15 seconds</span></span> | <span data-ttu-id="50204-531">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-532">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-533">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-534">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="50204-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="50204-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="50204-536">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-537">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-538">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-539">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="50204-540">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-540">Configure additional options</span></span>

<span data-ttu-id="50204-541">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="50204-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-542">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-543">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-543">.NET Option</span></span> |  <span data-ttu-id="50204-544">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-544">Default value</span></span> | <span data-ttu-id="50204-545">Description</span><span class="sxs-lookup"><span data-stu-id="50204-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="50204-546">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="50204-547">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-548">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-549">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="50204-550">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-550">Empty</span></span> | <span data-ttu-id="50204-551">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="50204-552">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-552">Empty</span></span> | <span data-ttu-id="50204-553">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="50204-554">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-554">Empty</span></span> | <span data-ttu-id="50204-555">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="50204-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="50204-556">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-556">5 seconds</span></span> | <span data-ttu-id="50204-557">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="50204-557">WebSockets only.</span></span> <span data-ttu-id="50204-558">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="50204-559">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="50204-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="50204-560">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-560">Empty</span></span> | <span data-ttu-id="50204-561">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="50204-562">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="50204-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="50204-563">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="50204-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="50204-564">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="50204-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="50204-565">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="50204-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="50204-566">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="50204-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="50204-567">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="50204-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="50204-568">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="50204-569">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="50204-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="50204-570">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="50204-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="50204-571">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="50204-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="50204-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-573">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-573">JavaScript Option</span></span> | <span data-ttu-id="50204-574">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-574">Default Value</span></span> | <span data-ttu-id="50204-575">Description</span><span class="sxs-lookup"><span data-stu-id="50204-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="50204-576">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="50204-577">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-578">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-579">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-580">Java</span><span class="sxs-lookup"><span data-stu-id="50204-580">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-581">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-581">Java Option</span></span> | <span data-ttu-id="50204-582">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-582">Default Value</span></span> | <span data-ttu-id="50204-583">Description</span><span class="sxs-lookup"><span data-stu-id="50204-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="50204-584">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="50204-585">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-586">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-587">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="50204-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="50204-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="50204-589">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-589">Empty</span></span> | <span data-ttu-id="50204-590">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="50204-591">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="50204-592">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="50204-593">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="50204-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="50204-594">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50204-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="50204-595">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="50204-596">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="50204-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="50204-597">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="50204-598">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="50204-599">`AddJsonProtocol`, içinde [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50204-600">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="50204-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="50204-601">Bu nesnedeki [Payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="50204-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="50204-602">Daha fazla bilgi için, [belgelerindekiSystem.Text.Js](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="50204-603">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50204-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="50204-604">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="50204-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="50204-605">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="50204-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="50204-606">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="50204-607">Newtonsoft.Jsüzerine geç</span><span class="sxs-lookup"><span data-stu-id="50204-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="50204-608">`Newtonsoft.Json`' De desteklenmeyen özelliklerine ihtiyacınız varsa `System.Text.Json` , bkz. [Newtonsoft.Jsüzerine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="50204-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="50204-609">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-609">MessagePack serialization options</span></span>

<span data-ttu-id="50204-610">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="50204-611">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="50204-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-612">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="50204-613">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-613">Configure server options</span></span>

<span data-ttu-id="50204-614">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="50204-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="50204-615">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-615">Option</span></span> | <span data-ttu-id="50204-616">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-616">Default Value</span></span> | <span data-ttu-id="50204-617">Description</span><span class="sxs-lookup"><span data-stu-id="50204-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="50204-618">30 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-618">30 seconds</span></span> | <span data-ttu-id="50204-619">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="50204-620">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="50204-621">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="50204-622">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-622">15 seconds</span></span> | <span data-ttu-id="50204-623">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="50204-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="50204-624">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-625">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-626">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-626">15 seconds</span></span> | <span data-ttu-id="50204-627">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="50204-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="50204-628">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="50204-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="50204-629">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="50204-630">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="50204-630">All installed protocols</span></span> | <span data-ttu-id="50204-631">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="50204-631">Protocols supported by this hub.</span></span> <span data-ttu-id="50204-632">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="50204-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="50204-633">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="50204-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="50204-634">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="50204-635">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="50204-636">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="50204-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="50204-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-637">32 KB</span></span> | <span data-ttu-id="50204-638">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="50204-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="50204-639">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="50204-640">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="50204-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="50204-641">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="50204-642">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="50204-643">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="50204-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="50204-644">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="50204-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="50204-645">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-645">Option</span></span> | <span data-ttu-id="50204-646">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-646">Default Value</span></span> | <span data-ttu-id="50204-647">Description</span><span class="sxs-lookup"><span data-stu-id="50204-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="50204-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-648">32 KB</span></span> | <span data-ttu-id="50204-649">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="50204-650">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="50204-651">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="50204-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="50204-652">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="50204-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="50204-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-653">32 KB</span></span> | <span data-ttu-id="50204-654">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="50204-655">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="50204-656">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="50204-656">All Transports are enabled.</span></span> | <span data-ttu-id="50204-657">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="50204-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="50204-658">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-658">See below.</span></span> | <span data-ttu-id="50204-659">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="50204-660">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-660">See below.</span></span> | <span data-ttu-id="50204-661">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="50204-662">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="50204-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="50204-663">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-663">Option</span></span> | <span data-ttu-id="50204-664">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-664">Default Value</span></span> | <span data-ttu-id="50204-665">Description</span><span class="sxs-lookup"><span data-stu-id="50204-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="50204-666">90 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-666">90 seconds</span></span> | <span data-ttu-id="50204-667">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="50204-668">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="50204-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="50204-669">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="50204-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="50204-670">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-670">Option</span></span> | <span data-ttu-id="50204-671">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-671">Default Value</span></span> | <span data-ttu-id="50204-672">Description</span><span class="sxs-lookup"><span data-stu-id="50204-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="50204-673">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-673">5 seconds</span></span> | <span data-ttu-id="50204-674">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="50204-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="50204-675">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="50204-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="50204-676">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="50204-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="50204-677">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-677">Configure client options</span></span>

<span data-ttu-id="50204-678">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="50204-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="50204-679">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="50204-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="50204-680">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-680">Configure logging</span></span>

<span data-ttu-id="50204-681">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="50204-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="50204-682">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="50204-683">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-684">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="50204-685">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="50204-686">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="50204-687">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="50204-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="50204-688">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="50204-689">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="50204-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="50204-690">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="50204-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="50204-691">Bir değer yerine `LogLevel` , bir `string` günlük düzeyi adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="50204-692">Bu SignalR , sabitlere erişiminizin olmadığı ortamlarda günlüğe kaydetme yapılandırılırken yararlı olur `LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="50204-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="50204-693">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="50204-693">The following table lists the available log levels.</span></span> <span data-ttu-id="50204-694">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="50204-695">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="50204-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="50204-696">Dize</span><span class="sxs-lookup"><span data-stu-id="50204-696">String</span></span>                      | <span data-ttu-id="50204-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="50204-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="50204-698">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="50204-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="50204-699">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="50204-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="50204-700">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="50204-701">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="50204-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="50204-702">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50204-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="50204-703">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="50204-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="50204-704">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="50204-705">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="50204-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="50204-706">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="50204-707">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-707">Configure allowed transports</span></span>

<span data-ttu-id="50204-708">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="50204-709">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="50204-710">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="50204-710">All transports are enabled by default.</span></span>

<span data-ttu-id="50204-711">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="50204-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="50204-712">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="50204-713">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="50204-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="50204-714">Java istemcisinde, taşıma yöntemi ile birlikte seçilir `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="50204-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="50204-715">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="50204-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="50204-716">SignalRJava istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="50204-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="50204-717">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-717">Configure bearer authentication</span></span>

<span data-ttu-id="50204-718">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="50204-719">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="50204-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="50204-720">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="50204-721">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="50204-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="50204-722">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="50204-723">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="50204-724">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="50204-725">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="50204-726">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="50204-727">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="50204-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="50204-728">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="50204-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-729">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-730">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-730">Option</span></span> | <span data-ttu-id="50204-731">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-731">Default value</span></span> | <span data-ttu-id="50204-732">Description</span><span class="sxs-lookup"><span data-stu-id="50204-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="50204-733">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-734">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-734">Timeout for server activity.</span></span> <span data-ttu-id="50204-735">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-736">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-737">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="50204-738">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-738">15 seconds</span></span> | <span data-ttu-id="50204-739">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-740">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-741">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-742">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-743">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-743">15 seconds</span></span> | <span data-ttu-id="50204-744">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-745">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-746">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="50204-747">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="50204-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="50204-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-749">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-749">Option</span></span> | <span data-ttu-id="50204-750">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-750">Default value</span></span> | <span data-ttu-id="50204-751">Description</span><span class="sxs-lookup"><span data-stu-id="50204-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="50204-752">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-753">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-753">Timeout for server activity.</span></span> <span data-ttu-id="50204-754">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="50204-755">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-756">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="50204-757">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-758">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-759">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-760">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-761">Java</span><span class="sxs-lookup"><span data-stu-id="50204-761">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-762">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-762">Option</span></span> | <span data-ttu-id="50204-763">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-763">Default value</span></span> | <span data-ttu-id="50204-764">Description</span><span class="sxs-lookup"><span data-stu-id="50204-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="50204-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="50204-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="50204-766">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-767">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-767">Timeout for server activity.</span></span> <span data-ttu-id="50204-768">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-769">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-770">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="50204-771">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-771">15 seconds</span></span> | <span data-ttu-id="50204-772">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-773">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-774">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-775">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="50204-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="50204-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="50204-777">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-778">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-779">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-780">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="50204-781">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-781">Configure additional options</span></span>

<span data-ttu-id="50204-782">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="50204-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-783">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-784">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-784">.NET Option</span></span> |  <span data-ttu-id="50204-785">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-785">Default value</span></span> | <span data-ttu-id="50204-786">Description</span><span class="sxs-lookup"><span data-stu-id="50204-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="50204-787">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="50204-788">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-789">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-790">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="50204-791">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-791">Empty</span></span> | <span data-ttu-id="50204-792">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="50204-793">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-793">Empty</span></span> | <span data-ttu-id="50204-794">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="50204-795">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-795">Empty</span></span> | <span data-ttu-id="50204-796">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="50204-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="50204-797">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-797">5 seconds</span></span> | <span data-ttu-id="50204-798">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="50204-798">WebSockets only.</span></span> <span data-ttu-id="50204-799">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="50204-800">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="50204-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="50204-801">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-801">Empty</span></span> | <span data-ttu-id="50204-802">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="50204-803">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="50204-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="50204-804">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="50204-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="50204-805">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="50204-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="50204-806">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="50204-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="50204-807">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="50204-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="50204-808">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="50204-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="50204-809">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="50204-810">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="50204-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="50204-811">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="50204-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="50204-812">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="50204-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="50204-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-814">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-814">JavaScript Option</span></span> | <span data-ttu-id="50204-815">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-815">Default Value</span></span> | <span data-ttu-id="50204-816">Description</span><span class="sxs-lookup"><span data-stu-id="50204-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="50204-817">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="50204-818">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-819">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-820">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-821">Java</span><span class="sxs-lookup"><span data-stu-id="50204-821">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-822">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-822">Java Option</span></span> | <span data-ttu-id="50204-823">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-823">Default Value</span></span> | <span data-ttu-id="50204-824">Description</span><span class="sxs-lookup"><span data-stu-id="50204-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="50204-825">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="50204-826">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-827">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-828">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="50204-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="50204-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="50204-830">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-830">Empty</span></span> | <span data-ttu-id="50204-831">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="50204-832">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="50204-833">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="50204-834">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="50204-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="50204-835">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50204-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="50204-836">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="50204-837">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="50204-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="50204-838">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="50204-839">JSON serileştirme, yöntekinizdeki [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="50204-840">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="50204-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="50204-841">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="50204-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="50204-842">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="50204-843">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50204-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="50204-844">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="50204-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="50204-845">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="50204-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="50204-846">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="50204-847">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-847">MessagePack serialization options</span></span>

<span data-ttu-id="50204-848">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="50204-849">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="50204-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-850">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="50204-851">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-851">Configure server options</span></span>

<span data-ttu-id="50204-852">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="50204-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="50204-853">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-853">Option</span></span> | <span data-ttu-id="50204-854">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-854">Default Value</span></span> | <span data-ttu-id="50204-855">Description</span><span class="sxs-lookup"><span data-stu-id="50204-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="50204-856">30 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-856">30 seconds</span></span> | <span data-ttu-id="50204-857">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="50204-858">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="50204-859">Önerilen değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="50204-860">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-860">15 seconds</span></span> | <span data-ttu-id="50204-861">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="50204-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="50204-862">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-863">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-864">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-864">15 seconds</span></span> | <span data-ttu-id="50204-865">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="50204-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="50204-866">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="50204-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="50204-867">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="50204-868">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="50204-868">All installed protocols</span></span> | <span data-ttu-id="50204-869">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="50204-869">Protocols supported by this hub.</span></span> <span data-ttu-id="50204-870">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="50204-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="50204-871">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="50204-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="50204-872">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="50204-873">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="50204-874">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="50204-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="50204-875">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="50204-876">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="50204-877">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="50204-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="50204-878">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="50204-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="50204-879">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-879">Option</span></span> | <span data-ttu-id="50204-880">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-880">Default Value</span></span> | <span data-ttu-id="50204-881">Description</span><span class="sxs-lookup"><span data-stu-id="50204-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="50204-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-882">32 KB</span></span> | <span data-ttu-id="50204-883">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="50204-884">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="50204-885">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="50204-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="50204-886">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="50204-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="50204-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-887">32 KB</span></span> | <span data-ttu-id="50204-888">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="50204-889">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="50204-890">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="50204-890">All Transports are enabled.</span></span> | <span data-ttu-id="50204-891">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="50204-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="50204-892">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-892">See below.</span></span> | <span data-ttu-id="50204-893">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="50204-894">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-894">See below.</span></span> | <span data-ttu-id="50204-895">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="50204-896">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="50204-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="50204-897">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-897">Option</span></span> | <span data-ttu-id="50204-898">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-898">Default Value</span></span> | <span data-ttu-id="50204-899">Description</span><span class="sxs-lookup"><span data-stu-id="50204-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="50204-900">90 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-900">90 seconds</span></span> | <span data-ttu-id="50204-901">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="50204-902">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="50204-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="50204-903">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="50204-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="50204-904">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-904">Option</span></span> | <span data-ttu-id="50204-905">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-905">Default Value</span></span> | <span data-ttu-id="50204-906">Description</span><span class="sxs-lookup"><span data-stu-id="50204-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="50204-907">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-907">5 seconds</span></span> | <span data-ttu-id="50204-908">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="50204-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="50204-909">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="50204-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="50204-910">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="50204-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="50204-911">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-911">Configure client options</span></span>

<span data-ttu-id="50204-912">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="50204-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="50204-913">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="50204-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="50204-914">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-914">Configure logging</span></span>

<span data-ttu-id="50204-915">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="50204-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="50204-916">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="50204-917">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-918">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="50204-919">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="50204-920">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="50204-921">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="50204-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="50204-922">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="50204-923">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="50204-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="50204-924">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="50204-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="50204-925">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="50204-926">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="50204-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="50204-927">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50204-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="50204-928">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="50204-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="50204-929">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="50204-930">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="50204-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="50204-931">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="50204-932">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-932">Configure allowed transports</span></span>

<span data-ttu-id="50204-933">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="50204-934">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="50204-935">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="50204-935">All transports are enabled by default.</span></span>

<span data-ttu-id="50204-936">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="50204-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="50204-937">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="50204-938">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="50204-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="50204-939">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-939">Configure bearer authentication</span></span>

<span data-ttu-id="50204-940">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="50204-941">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="50204-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="50204-942">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="50204-943">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="50204-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="50204-944">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="50204-945">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="50204-946">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="50204-947">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="50204-948">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="50204-949">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="50204-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="50204-950">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="50204-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-951">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-952">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-952">Option</span></span> | <span data-ttu-id="50204-953">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-953">Default value</span></span> | <span data-ttu-id="50204-954">Description</span><span class="sxs-lookup"><span data-stu-id="50204-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="50204-955">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-956">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-956">Timeout for server activity.</span></span> <span data-ttu-id="50204-957">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-958">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-959">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="50204-960">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-960">15 seconds</span></span> | <span data-ttu-id="50204-961">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-962">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-963">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-964">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-965">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-965">15 seconds</span></span> | <span data-ttu-id="50204-966">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-967">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-968">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="50204-969">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="50204-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="50204-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-971">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-971">Option</span></span> | <span data-ttu-id="50204-972">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-972">Default value</span></span> | <span data-ttu-id="50204-973">Description</span><span class="sxs-lookup"><span data-stu-id="50204-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="50204-974">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-975">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-975">Timeout for server activity.</span></span> <span data-ttu-id="50204-976">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="50204-977">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-978">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="50204-979">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-980">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-981">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-982">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-983">Java</span><span class="sxs-lookup"><span data-stu-id="50204-983">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-984">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-984">Option</span></span> | <span data-ttu-id="50204-985">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-985">Default value</span></span> | <span data-ttu-id="50204-986">Description</span><span class="sxs-lookup"><span data-stu-id="50204-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="50204-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="50204-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="50204-988">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-989">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-989">Timeout for server activity.</span></span> <span data-ttu-id="50204-990">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-991">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-992">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="50204-993">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-993">15 seconds</span></span> | <span data-ttu-id="50204-994">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-995">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-996">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-997">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="50204-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="50204-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="50204-999">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="50204-1000">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="50204-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="50204-1001">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="50204-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="50204-1002">İstemci, sunucuda küme içinde bir ileti göndermediyse `ClientTimeoutInterval` sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="50204-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="50204-1003">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1003">Configure additional options</span></span>

<span data-ttu-id="50204-1004">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="50204-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-1006">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1006">.NET Option</span></span> |  <span data-ttu-id="50204-1007">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1007">Default value</span></span> | <span data-ttu-id="50204-1008">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="50204-1009">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="50204-1010">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1011">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1012">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="50204-1013">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1013">Empty</span></span> | <span data-ttu-id="50204-1014">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="50204-1015">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1015">Empty</span></span> | <span data-ttu-id="50204-1016">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="50204-1017">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1017">Empty</span></span> | <span data-ttu-id="50204-1018">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="50204-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="50204-1019">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1019">5 seconds</span></span> | <span data-ttu-id="50204-1020">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="50204-1020">WebSockets only.</span></span> <span data-ttu-id="50204-1021">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="50204-1022">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="50204-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="50204-1023">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1023">Empty</span></span> | <span data-ttu-id="50204-1024">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="50204-1025">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="50204-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="50204-1026">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="50204-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="50204-1027">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="50204-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="50204-1028">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="50204-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="50204-1029">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="50204-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="50204-1030">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="50204-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="50204-1031">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="50204-1032">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="50204-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="50204-1033">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="50204-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="50204-1034">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="50204-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="50204-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-1036">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1036">JavaScript Option</span></span> | <span data-ttu-id="50204-1037">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1037">Default Value</span></span> | <span data-ttu-id="50204-1038">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="50204-1039">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="50204-1040">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1041">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1042">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-1043">Java</span><span class="sxs-lookup"><span data-stu-id="50204-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-1044">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1044">Java Option</span></span> | <span data-ttu-id="50204-1045">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1045">Default Value</span></span> | <span data-ttu-id="50204-1046">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="50204-1047">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="50204-1048">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1049">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1050">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="50204-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="50204-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="50204-1052">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1052">Empty</span></span> | <span data-ttu-id="50204-1053">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="50204-1054">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="50204-1055">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="50204-1056">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="50204-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="50204-1057">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50204-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="50204-1058">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="50204-1059">ASP.NET Core SignalR , kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="50204-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="50204-1060">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="50204-1061">JSON serileştirme, yöntekinizdeki [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="50204-1062">Yöntemi, bir `AddJsonProtocol` nesneyi alan bir temsilciyi alır `options` .</span><span class="sxs-lookup"><span data-stu-id="50204-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="50204-1063">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve dönüş değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="50204-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="50204-1064">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="50204-1065">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50204-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="50204-1066">.NET istemcisinde, `AddJsonProtocol` [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="50204-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="50204-1067">`Microsoft.Extensions.DependencyInjection`Uzantı metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="50204-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="50204-1068">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="50204-1069">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-1069">MessagePack serialization options</span></span>

<span data-ttu-id="50204-1070">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="50204-1071">Daha ayrıntılı bilgi için [' de MessagePack ' SignalR ](xref:signalr/messagepackhubprotocol) i inceleyin.</span><span class="sxs-lookup"><span data-stu-id="50204-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-1072">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="50204-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="50204-1073">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1073">Configure server options</span></span>

<span data-ttu-id="50204-1074">Aşağıdaki tabloda, hub 'ları yapılandırmaya yönelik seçenekler açıklanmaktadır SignalR :</span><span class="sxs-lookup"><span data-stu-id="50204-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="50204-1075">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1075">Option</span></span> | <span data-ttu-id="50204-1076">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1076">Default Value</span></span> | <span data-ttu-id="50204-1077">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="50204-1078">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1078">15 seconds</span></span> | <span data-ttu-id="50204-1079">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="50204-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="50204-1080">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-1081">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="50204-1082">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1082">15 seconds</span></span> | <span data-ttu-id="50204-1083">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="50204-1084">Değişiklik yaparken `KeepAliveInterval` , `ServerTimeout` / `serverTimeoutInMilliseconds` istemcideki ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="50204-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="50204-1085">Önerilen `ServerTimeout` / `serverTimeoutInMilliseconds` değer Double değeridir `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="50204-1086">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="50204-1086">All installed protocols</span></span> | <span data-ttu-id="50204-1087">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="50204-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="50204-1088">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="50204-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="50204-1089">İse `true` , bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="50204-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="50204-1090">Varsayılan olarak, `false` Bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="50204-1091">Seçenekler, içindeki çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50204-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="50204-1092">Tek bir hub için Seçenekler ' de belirtilen genel seçenekleri geçersiz kılar `AddSignalR` ve kullanılarak yapılandırılabilir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="50204-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="50204-1093">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="50204-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="50204-1094">`HttpConnectionDispatcherOptions`Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="50204-1095">Bu seçenekler, içinde [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 'ya bir temsilci geçirilerek yapılandırılır `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="50204-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="50204-1096">Aşağıdaki tabloda ASP.NET Core SignalR GELIŞMIŞ http seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="50204-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="50204-1097">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1097">Option</span></span> | <span data-ttu-id="50204-1098">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1098">Default Value</span></span> | <span data-ttu-id="50204-1099">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="50204-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-1100">32 KB</span></span> | <span data-ttu-id="50204-1101">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="50204-1102">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="50204-1103">Veriler, `Authorize` hub sınıfına uygulanan özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="50204-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="50204-1104">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="50204-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="50204-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="50204-1105">32 KB</span></span> | <span data-ttu-id="50204-1106">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="50204-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="50204-1107">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="50204-1108">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="50204-1108">All Transports are enabled.</span></span> | <span data-ttu-id="50204-1109">Bir `HttpTransportType` istemcinin bağlanmak için kullanabileceği taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="50204-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="50204-1110">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1110">See below.</span></span> | <span data-ttu-id="50204-1111">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="50204-1112">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1112">See below.</span></span> | <span data-ttu-id="50204-1113">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="50204-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="50204-1114">Uzun yoklama taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="50204-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="50204-1115">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1115">Option</span></span> | <span data-ttu-id="50204-1116">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1116">Default Value</span></span> | <span data-ttu-id="50204-1117">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="50204-1118">90 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1118">90 seconds</span></span> | <span data-ttu-id="50204-1119">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="50204-1120">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="50204-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="50204-1121">WebSocket taşıması, özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="50204-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="50204-1122">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1122">Option</span></span> | <span data-ttu-id="50204-1123">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1123">Default Value</span></span> | <span data-ttu-id="50204-1124">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="50204-1125">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1125">5 seconds</span></span> | <span data-ttu-id="50204-1126">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="50204-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="50204-1127">Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci `Sec-WebSocket-Protocol` .</span><span class="sxs-lookup"><span data-stu-id="50204-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="50204-1128">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="50204-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="50204-1129">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1129">Configure client options</span></span>

<span data-ttu-id="50204-1130">İstemci seçenekleri tür üzerinde yapılandırılabilir `HubConnectionBuilder` (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="50204-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="50204-1131">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra kendisi de içerir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="50204-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="50204-1132">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1132">Configure logging</span></span>

<span data-ttu-id="50204-1133">Günlüğe kaydetme, .NET Istemcisinde yöntemi kullanılarak yapılandırılır `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="50204-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="50204-1134">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="50204-1135">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="50204-1136">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="50204-1137">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="50204-1138">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="50204-1139">`AddConsole`Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="50204-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="50204-1140">JavaScript istemcisinde, benzer bir `configureLogging` Yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="50204-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="50204-1141">`LogLevel`Üretilecek günlük iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="50204-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="50204-1142">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="50204-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="50204-1143">Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="50204-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="50204-1144">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [ SignalR Tanılama belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="50204-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="50204-1145">SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="50204-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="50204-1146">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="50204-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="50204-1147">Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="50204-1148">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="50204-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="50204-1149">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="50204-1150">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1150">Configure allowed transports</span></span>

<span data-ttu-id="50204-1151">Tarafından kullanılan aktarımlar, SignalR `WithUrl` çağrıda ( `withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="50204-1152">Bir bit düzeyinde OR değeri, `HttpTransportType` istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="50204-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="50204-1153">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="50204-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="50204-1154">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="50204-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="50204-1155">JavaScript istemcisinde aktarımlar, `transport` için belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="50204-1156">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1156">Configure bearer authentication</span></span>

<span data-ttu-id="50204-1157">Kimlik doğrulama verilerini isteklerle birlikte sağlamak için SignalR , `AccessTokenProvider` `accessTokenFactory` istenen erişim belirtecini döndüren bir işlevi belirtmek Için seçeneğini (JavaScript 'te) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="50204-1158">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir ( `Authorization` bir türü olan üst bilgi kullanılarak `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="50204-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="50204-1159">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50204-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="50204-1160">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri olarak sağlanır `access_token` .</span><span class="sxs-lookup"><span data-stu-id="50204-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="50204-1161">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki seçenekler temsilcisi kullanılarak belirtilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="50204-1162">JavaScript istemcisinde erişim belirteci, `accessTokenFactory` içindeki seçenekler nesnesindeki alanı ayarlanarak yapılandırılır `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="50204-1163">SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="50204-1164">Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="50204-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="50204-1165">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="50204-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="50204-1166">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="50204-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="50204-1167">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="50204-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-1169">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1169">Option</span></span> | <span data-ttu-id="50204-1170">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1170">Default value</span></span> | <span data-ttu-id="50204-1171">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="50204-1172">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-1173">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-1173">Timeout for server activity.</span></span> <span data-ttu-id="50204-1174">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-1175">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-1176">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="50204-1177">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1177">15 seconds</span></span> | <span data-ttu-id="50204-1178">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-1179">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler ( `onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="50204-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="50204-1180">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-1181">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="50204-1182">.NET Istemcisinde zaman aşımı değerleri değer olarak belirtilir `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="50204-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="50204-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-1184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1184">Option</span></span> | <span data-ttu-id="50204-1185">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1185">Default value</span></span> | <span data-ttu-id="50204-1186">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="50204-1187">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-1188">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-1188">Timeout for server activity.</span></span> <span data-ttu-id="50204-1189">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="50204-1190">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-1191">Önerilen değer, ping için en az iki sunucu değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-1192">Java</span><span class="sxs-lookup"><span data-stu-id="50204-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-1193">Seçenek</span><span class="sxs-lookup"><span data-stu-id="50204-1193">Option</span></span> | <span data-ttu-id="50204-1194">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1194">Default value</span></span> | <span data-ttu-id="50204-1195">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="50204-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="50204-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="50204-1197">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="50204-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="50204-1198">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-1198">Timeout for server activity.</span></span> <span data-ttu-id="50204-1199">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-1200">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="50204-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="50204-1201">Önerilen değer, ping bir sürenin gelmesi için en az iki değerin değeri olan bir sayıdır `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="50204-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="50204-1202">15 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1202">15 seconds</span></span> | <span data-ttu-id="50204-1203">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="50204-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="50204-1204">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="50204-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="50204-1205">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="50204-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="50204-1206">El sıkışma işlemi hakkında daha fazla bilgi için [ SignalR hub protokol belirtimine](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="50204-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="50204-1207">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="50204-1207">Configure additional options</span></span>

<span data-ttu-id="50204-1208">`WithUrl` `withUrl` `HubConnectionBuilder` Java istemcisinde Içindeki çeşitli yapılandırma API 'lerinde veya üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir `HttpHubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="50204-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="50204-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="50204-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="50204-1210">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1210">.NET Option</span></span> |  <span data-ttu-id="50204-1211">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1211">Default value</span></span> | <span data-ttu-id="50204-1212">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="50204-1213">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="50204-1214">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1215">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1216">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="50204-1217">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1217">Empty</span></span> | <span data-ttu-id="50204-1218">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="50204-1219">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1219">Empty</span></span> | <span data-ttu-id="50204-1220">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="50204-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="50204-1221">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1221">Empty</span></span> | <span data-ttu-id="50204-1222">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="50204-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="50204-1223">5 saniye</span><span class="sxs-lookup"><span data-stu-id="50204-1223">5 seconds</span></span> | <span data-ttu-id="50204-1224">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="50204-1224">WebSockets only.</span></span> <span data-ttu-id="50204-1225">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="50204-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="50204-1226">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="50204-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="50204-1227">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1227">Empty</span></span> | <span data-ttu-id="50204-1228">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="50204-1229">HTTP istekleri göndermek için kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="50204-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="50204-1230">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="50204-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="50204-1231">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="50204-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="50204-1232">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni bir `HttpMessageHandler` örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="50204-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="50204-1233">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="50204-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="50204-1234">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="50204-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="50204-1235">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="50204-1236">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="50204-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="50204-1237">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="50204-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="50204-1238">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="50204-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="50204-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="50204-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="50204-1240">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1240">JavaScript Option</span></span> | <span data-ttu-id="50204-1241">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1241">Default Value</span></span> | <span data-ttu-id="50204-1242">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="50204-1243">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="50204-1244">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1245">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1246">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="50204-1247">Java</span><span class="sxs-lookup"><span data-stu-id="50204-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="50204-1248">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="50204-1248">Java Option</span></span> | <span data-ttu-id="50204-1249">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="50204-1249">Default Value</span></span> | <span data-ttu-id="50204-1250">Description</span><span class="sxs-lookup"><span data-stu-id="50204-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="50204-1251">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="50204-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="50204-1252">`true`Anlaşma adımını atlamak için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="50204-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="50204-1253">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="50204-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="50204-1254">Bu ayar, Azure hizmeti kullanılırken etkinleştirilemez SignalR .</span><span class="sxs-lookup"><span data-stu-id="50204-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="50204-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="50204-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="50204-1256">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="50204-1256">Empty</span></span> | <span data-ttu-id="50204-1257">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="50204-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="50204-1258">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için belirtilen seçenekler temsilcisinden değiştirilebilir `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="50204-1259">JavaScript Istemcisinde, bu seçenekler şu şekilde sağlanmış bir JavaScript nesnesi içinde bulunabilir `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="50204-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="50204-1260">Java istemcisinde, bu seçenekler, içindeki döndürülen yöntemlerle yapılandırılabilir `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="50204-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="50204-1261">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="50204-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
