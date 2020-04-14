---
title: ASP.NET SignalR Core yapılandırması
author: bradygaster
description: ASP.NET Core SignalR uygulamalarını nasıl yapılandırıştırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228146"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="14961-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="14961-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14961-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14961-105">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="14961-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14961-106">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14961-107">JSON [serileştirme, AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="14961-108">`AddJsonProtocol`[addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) sonra `Startup.ConfigureServices`eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14961-109">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="14961-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14961-110">Bu nesnedeki [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="14961-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14961-111">Daha fazla bilgi için [System.Text.Json belgelerine](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="14961-112">Örnek olarak, varsayılan "camelCase" adları yerine özellik adlarının kasasını değiştirmemek için serileştiriciyi yapılandırmak `Startup.ConfigureServices`için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="14961-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="14961-113">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14961-114">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="14961-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14961-115">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="14961-116">Newtonsoft.Json'a geç</span><span class="sxs-lookup"><span data-stu-id="14961-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="14961-117">Eğer bu özellikleri `Newtonsoft.Json` desteklenmez `System.Text.Json`gerekiyorsa, [Newtonsoft.Json geçiş](xref:migration/22-to-30#switch-to-newtonsoftjson)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14961-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-118">MessagePack serialization options</span></span>

<span data-ttu-id="14961-119">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14961-120">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-121">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14961-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-122">Configure server options</span></span>

<span data-ttu-id="14961-123">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14961-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-124">Option</span></span> | <span data-ttu-id="14961-125">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-125">Default Value</span></span> | <span data-ttu-id="14961-126">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14961-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-127">30 seconds</span></span> | <span data-ttu-id="14961-128">Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14961-129">Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14961-130">Önerilen değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14961-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-131">15 seconds</span></span> | <span data-ttu-id="14961-132">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="14961-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14961-133">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-134">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-135">15 seconds</span></span> | <span data-ttu-id="14961-136">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14961-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14961-137">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="14961-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14961-138">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14961-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="14961-139">All installed protocols</span></span> | <span data-ttu-id="14961-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="14961-140">Protocols supported by this hub.</span></span> <span data-ttu-id="14961-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14961-142">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="14961-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14961-143">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="14961-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="14961-144">İstemci yükleme akışları için arabelleğe alınabilecek maksimum öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="14961-145">Bu sınıra ulaşılırsa, sunucu öğeleri akış işlemlerine kadar çağrıların işlenmesi engellenir.</span><span class="sxs-lookup"><span data-stu-id="14961-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="14961-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-146">32 KB</span></span> | <span data-ttu-id="14961-147">Tek bir gelen hub iletisinin maksimum boyutu.</span><span class="sxs-lookup"><span data-stu-id="14961-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="14961-148">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14961-149">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14961-150">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14961-151">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="14961-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14961-152">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="14961-153">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14961-154">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-154">Option</span></span> | <span data-ttu-id="14961-155">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-155">Default Value</span></span> | <span data-ttu-id="14961-156">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14961-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-157">32 KB</span></span> | <span data-ttu-id="14961-158">Arka basınç uygulamadan önce sunucunun arabellek yaptığı istemciden alınan maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="14961-159">Bu değeri artırmak, sunucunun geri baskı uygulamadan daha büyük iletileri daha hızlı almasını sağlar, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14961-160">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="14961-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14961-161">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="14961-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14961-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-162">32 KB</span></span> | <span data-ttu-id="14961-163">Arka basıncı gözlemlemeden önce sunucunun arabellek yaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="14961-164">Bu değeri artırmak, sunucunun daha büyük iletileri geri baskı beklemeden daha hızlı arabelleğe almamasına olanak tanır, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14961-165">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="14961-165">All Transports are enabled.</span></span> | <span data-ttu-id="14961-166">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="14961-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14961-167">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-167">See below.</span></span> | <span data-ttu-id="14961-168">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14961-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-169">See below.</span></span> | <span data-ttu-id="14961-170">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="14961-171">0</span><span class="sxs-lookup"><span data-stu-id="14961-171">0</span></span> | <span data-ttu-id="14961-172">Anlaşma protokolünün minimum sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="14961-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="14961-173">Bu, istemcileri daha yeni sürümlere sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="14961-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="14961-174">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14961-175">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-175">Option</span></span> | <span data-ttu-id="14961-176">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-176">Default Value</span></span> | <span data-ttu-id="14961-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14961-178">90 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-178">90 seconds</span></span> | <span data-ttu-id="14961-179">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14961-180">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14961-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14961-181">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14961-182">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-182">Option</span></span> | <span data-ttu-id="14961-183">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-183">Default Value</span></span> | <span data-ttu-id="14961-184">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14961-185">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-185">5 seconds</span></span> | <span data-ttu-id="14961-186">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14961-187">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14961-188">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="14961-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14961-189">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-189">Configure client options</span></span>

<span data-ttu-id="14961-190">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="14961-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14961-191">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="14961-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14961-192">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-192">Configure logging</span></span>

<span data-ttu-id="14961-193">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14961-194">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14961-195">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-196">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="14961-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14961-197">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14961-198">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="14961-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14961-199">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="14961-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14961-200">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14961-201">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14961-202">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="14961-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="14961-203">Bir `LogLevel` değer yerine, günlük düzeyi `string` adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="14961-204">Bu, `LogLevel` sabitlere erişiminiz olmayan ortamlarda SignalR günlüğe kaydetmeyi yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="14961-205">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="14961-205">The following table lists the available log levels.</span></span> <span data-ttu-id="14961-206">Günlüğe kaydedilecek `configureLogging` **minimum** günlük düzeyini ayarlar için sağladığınız değer.</span><span class="sxs-lookup"><span data-stu-id="14961-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="14961-207">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="14961-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="14961-208">Dize</span><span class="sxs-lookup"><span data-stu-id="14961-208">String</span></span>                      | <span data-ttu-id="14961-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="14961-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="14961-210">`info`**veya**`information`</span><span class="sxs-lookup"><span data-stu-id="14961-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="14961-211">`warn`**veya**`warning`</span><span class="sxs-lookup"><span data-stu-id="14961-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="14961-212">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="14961-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14961-213">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14961-214">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14961-215">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="14961-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14961-216">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="14961-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14961-217">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="14961-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14961-218">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14961-219">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-219">Configure allowed transports</span></span>

<span data-ttu-id="14961-220">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14961-221">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14961-222">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="14961-222">All transports are enabled by default.</span></span>

<span data-ttu-id="14961-223">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="14961-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14961-224">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14961-225">Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.</span><span class="sxs-lookup"><span data-stu-id="14961-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="14961-226">Java istemcisinde, aktarım `withTransport` `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="14961-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="14961-227">Java istemcisi varsayılan olarak WebSockets aktarımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14961-228">SignalR Java istemcisi henüz geri dönüş taşıma yı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="14961-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14961-229">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-229">Configure bearer authentication</span></span>

<span data-ttu-id="14961-230">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14961-231">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="14961-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14961-232">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="14961-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14961-233">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="14961-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14961-234">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14961-235">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="14961-236">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14961-237">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14961-238">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14961-239">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14961-240">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-241">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-242">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-242">Option</span></span> | <span data-ttu-id="14961-243">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-243">Default value</span></span> | <span data-ttu-id="14961-244">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14961-245">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-246">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-246">Timeout for server activity.</span></span> <span data-ttu-id="14961-247">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="14961-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-248">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-249">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14961-250">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-250">15 seconds</span></span> | <span data-ttu-id="14961-251">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-252">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-253">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-254">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-255">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-255">15 seconds</span></span> | <span data-ttu-id="14961-256">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-257">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-258">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14961-259">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="14961-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14961-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-261">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-261">Option</span></span> | <span data-ttu-id="14961-262">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-262">Default value</span></span> | <span data-ttu-id="14961-263">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14961-264">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-265">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-265">Timeout for server activity.</span></span> <span data-ttu-id="14961-266">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14961-267">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-268">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14961-269">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-270">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-271">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-272">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-273">Java</span><span class="sxs-lookup"><span data-stu-id="14961-273">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-274">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-274">Option</span></span> | <span data-ttu-id="14961-275">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-275">Default value</span></span> | <span data-ttu-id="14961-276">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14961-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14961-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14961-278">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-279">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-279">Timeout for server activity.</span></span> <span data-ttu-id="14961-280">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-281">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-282">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14961-283">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-283">15 seconds</span></span> | <span data-ttu-id="14961-284">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-285">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-286">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-287">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14961-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14961-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14961-289">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-290">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-291">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-292">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14961-293">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-293">Configure additional options</span></span>

<span data-ttu-id="14961-294">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-295">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-296">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-296">.NET Option</span></span> |  <span data-ttu-id="14961-297">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-297">Default value</span></span> | <span data-ttu-id="14961-298">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14961-299">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14961-300">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-301">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-302">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14961-303">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-303">Empty</span></span> | <span data-ttu-id="14961-304">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14961-305">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-305">Empty</span></span> | <span data-ttu-id="14961-306">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14961-307">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-307">Empty</span></span> | <span data-ttu-id="14961-308">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="14961-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14961-309">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-309">5 seconds</span></span> | <span data-ttu-id="14961-310">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14961-310">WebSockets only.</span></span> <span data-ttu-id="14961-311">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14961-312">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="14961-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14961-313">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-313">Empty</span></span> | <span data-ttu-id="14961-314">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14961-315">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14961-316">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="14961-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="14961-317">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="14961-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14961-318">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="14961-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14961-319">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="14961-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14961-320">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="14961-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14961-321">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14961-322">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="14961-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14961-323">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14961-324">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="14961-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14961-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-326">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-326">JavaScript Option</span></span> | <span data-ttu-id="14961-327">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-327">Default Value</span></span> | <span data-ttu-id="14961-328">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14961-329">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14961-330">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-331">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-332">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-333">Java</span><span class="sxs-lookup"><span data-stu-id="14961-333">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-334">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-334">Java Option</span></span> | <span data-ttu-id="14961-335">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-335">Default Value</span></span> | <span data-ttu-id="14961-336">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14961-337">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14961-338">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-339">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-340">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14961-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14961-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14961-342">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-342">Empty</span></span> | <span data-ttu-id="14961-343">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14961-344">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14961-345">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14961-346">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14961-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14961-347">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14961-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14961-348">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14961-349">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="14961-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14961-350">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14961-351">JSON [serileştirme, AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="14961-352">`AddJsonProtocol`[addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) sonra `Startup.ConfigureServices`eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14961-353">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="14961-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14961-354">Bu nesnedeki [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="14961-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14961-355">Daha fazla bilgi için [System.Text.Json belgelerine](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="14961-356">Örnek olarak, varsayılan "camelCase" adları yerine özellik adlarının kasasını değiştirmemek için serileştiriciyi yapılandırmak `Startup.ConfigureServices`için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="14961-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="14961-357">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14961-358">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="14961-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14961-359">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="14961-360">Newtonsoft.Json'a geç</span><span class="sxs-lookup"><span data-stu-id="14961-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="14961-361">Eğer bu özellikleri `Newtonsoft.Json` desteklenmez `System.Text.Json`gerekiyorsa, [Newtonsoft.Json geçiş](xref:migration/22-to-30#switch-to-newtonsoftjson)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14961-362">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-362">MessagePack serialization options</span></span>

<span data-ttu-id="14961-363">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14961-364">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-365">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14961-366">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-366">Configure server options</span></span>

<span data-ttu-id="14961-367">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14961-368">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-368">Option</span></span> | <span data-ttu-id="14961-369">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-369">Default Value</span></span> | <span data-ttu-id="14961-370">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14961-371">30 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-371">30 seconds</span></span> | <span data-ttu-id="14961-372">Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14961-373">Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14961-374">Önerilen değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14961-375">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-375">15 seconds</span></span> | <span data-ttu-id="14961-376">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="14961-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14961-377">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-378">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-379">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-379">15 seconds</span></span> | <span data-ttu-id="14961-380">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14961-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14961-381">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="14961-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14961-382">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14961-383">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="14961-383">All installed protocols</span></span> | <span data-ttu-id="14961-384">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="14961-384">Protocols supported by this hub.</span></span> <span data-ttu-id="14961-385">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14961-386">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="14961-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14961-387">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="14961-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="14961-388">İstemci yükleme akışları için arabelleğe alınabilecek maksimum öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="14961-389">Bu sınıra ulaşılırsa, sunucu öğeleri akış işlemlerine kadar çağrıların işlenmesi engellenir.</span><span class="sxs-lookup"><span data-stu-id="14961-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="14961-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-390">32 KB</span></span> | <span data-ttu-id="14961-391">Tek bir gelen hub iletisinin maksimum boyutu.</span><span class="sxs-lookup"><span data-stu-id="14961-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="14961-392">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14961-393">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14961-394">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14961-395">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="14961-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14961-396">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="14961-397">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14961-398">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-398">Option</span></span> | <span data-ttu-id="14961-399">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-399">Default Value</span></span> | <span data-ttu-id="14961-400">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14961-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-401">32 KB</span></span> | <span data-ttu-id="14961-402">Arka basınç uygulamadan önce sunucunun arabellek yaptığı istemciden alınan maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="14961-403">Bu değeri artırmak, sunucunun geri baskı uygulamadan daha büyük iletileri daha hızlı almasını sağlar, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14961-404">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="14961-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14961-405">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="14961-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14961-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-406">32 KB</span></span> | <span data-ttu-id="14961-407">Arka basıncı gözlemlemeden önce sunucunun arabellek yaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="14961-408">Bu değeri artırmak, sunucunun daha büyük iletileri geri baskı beklemeden daha hızlı arabelleğe almamasına olanak tanır, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14961-409">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="14961-409">All Transports are enabled.</span></span> | <span data-ttu-id="14961-410">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="14961-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14961-411">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-411">See below.</span></span> | <span data-ttu-id="14961-412">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14961-413">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-413">See below.</span></span> | <span data-ttu-id="14961-414">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14961-415">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14961-416">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-416">Option</span></span> | <span data-ttu-id="14961-417">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-417">Default Value</span></span> | <span data-ttu-id="14961-418">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14961-419">90 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-419">90 seconds</span></span> | <span data-ttu-id="14961-420">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14961-421">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14961-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14961-422">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14961-423">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-423">Option</span></span> | <span data-ttu-id="14961-424">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-424">Default Value</span></span> | <span data-ttu-id="14961-425">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14961-426">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-426">5 seconds</span></span> | <span data-ttu-id="14961-427">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14961-428">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14961-429">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="14961-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14961-430">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-430">Configure client options</span></span>

<span data-ttu-id="14961-431">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="14961-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14961-432">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="14961-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14961-433">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-433">Configure logging</span></span>

<span data-ttu-id="14961-434">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14961-435">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14961-436">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-437">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="14961-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14961-438">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14961-439">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="14961-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14961-440">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="14961-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14961-441">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14961-442">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14961-443">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="14961-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="14961-444">Bir `LogLevel` değer yerine, günlük düzeyi `string` adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="14961-445">Bu, `LogLevel` sabitlere erişiminiz olmayan ortamlarda SignalR günlüğe kaydetmeyi yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="14961-446">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="14961-446">The following table lists the available log levels.</span></span> <span data-ttu-id="14961-447">Günlüğe kaydedilecek `configureLogging` **minimum** günlük düzeyini ayarlar için sağladığınız değer.</span><span class="sxs-lookup"><span data-stu-id="14961-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="14961-448">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="14961-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="14961-449">Dize</span><span class="sxs-lookup"><span data-stu-id="14961-449">String</span></span>                      | <span data-ttu-id="14961-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="14961-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="14961-451">`info`**veya**`information`</span><span class="sxs-lookup"><span data-stu-id="14961-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="14961-452">`warn`**veya**`warning`</span><span class="sxs-lookup"><span data-stu-id="14961-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="14961-453">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="14961-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14961-454">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14961-455">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14961-456">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="14961-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14961-457">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="14961-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14961-458">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="14961-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14961-459">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14961-460">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-460">Configure allowed transports</span></span>

<span data-ttu-id="14961-461">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14961-462">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14961-463">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="14961-463">All transports are enabled by default.</span></span>

<span data-ttu-id="14961-464">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="14961-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14961-465">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14961-466">Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.</span><span class="sxs-lookup"><span data-stu-id="14961-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="14961-467">Java istemcisinde, aktarım `withTransport` `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="14961-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="14961-468">Java istemcisi varsayılan olarak WebSockets aktarımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14961-469">SignalR Java istemcisi henüz geri dönüş taşıma yı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="14961-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14961-470">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-470">Configure bearer authentication</span></span>

<span data-ttu-id="14961-471">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14961-472">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="14961-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14961-473">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="14961-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14961-474">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="14961-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14961-475">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14961-476">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="14961-477">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14961-478">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14961-479">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14961-480">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14961-481">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-482">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-483">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-483">Option</span></span> | <span data-ttu-id="14961-484">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-484">Default value</span></span> | <span data-ttu-id="14961-485">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14961-486">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-487">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-487">Timeout for server activity.</span></span> <span data-ttu-id="14961-488">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="14961-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-489">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-490">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14961-491">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-491">15 seconds</span></span> | <span data-ttu-id="14961-492">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-493">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-494">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-495">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-496">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-496">15 seconds</span></span> | <span data-ttu-id="14961-497">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-498">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-499">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14961-500">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="14961-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14961-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-502">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-502">Option</span></span> | <span data-ttu-id="14961-503">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-503">Default value</span></span> | <span data-ttu-id="14961-504">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14961-505">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-506">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-506">Timeout for server activity.</span></span> <span data-ttu-id="14961-507">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14961-508">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-509">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14961-510">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-511">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-512">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-513">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-514">Java</span><span class="sxs-lookup"><span data-stu-id="14961-514">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-515">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-515">Option</span></span> | <span data-ttu-id="14961-516">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-516">Default value</span></span> | <span data-ttu-id="14961-517">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14961-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14961-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14961-519">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-520">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-520">Timeout for server activity.</span></span> <span data-ttu-id="14961-521">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-522">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-523">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14961-524">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-524">15 seconds</span></span> | <span data-ttu-id="14961-525">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-526">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-527">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-528">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14961-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14961-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14961-530">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-531">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-532">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-533">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14961-534">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-534">Configure additional options</span></span>

<span data-ttu-id="14961-535">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-536">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-537">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-537">.NET Option</span></span> |  <span data-ttu-id="14961-538">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-538">Default value</span></span> | <span data-ttu-id="14961-539">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14961-540">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14961-541">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-542">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-543">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14961-544">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-544">Empty</span></span> | <span data-ttu-id="14961-545">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14961-546">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-546">Empty</span></span> | <span data-ttu-id="14961-547">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14961-548">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-548">Empty</span></span> | <span data-ttu-id="14961-549">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="14961-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14961-550">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-550">5 seconds</span></span> | <span data-ttu-id="14961-551">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14961-551">WebSockets only.</span></span> <span data-ttu-id="14961-552">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14961-553">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="14961-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14961-554">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-554">Empty</span></span> | <span data-ttu-id="14961-555">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14961-556">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14961-557">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="14961-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="14961-558">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="14961-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14961-559">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="14961-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14961-560">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="14961-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14961-561">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="14961-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14961-562">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14961-563">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="14961-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14961-564">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14961-565">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="14961-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14961-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-567">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-567">JavaScript Option</span></span> | <span data-ttu-id="14961-568">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-568">Default Value</span></span> | <span data-ttu-id="14961-569">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14961-570">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14961-571">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-572">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-573">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-574">Java</span><span class="sxs-lookup"><span data-stu-id="14961-574">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-575">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-575">Java Option</span></span> | <span data-ttu-id="14961-576">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-576">Default Value</span></span> | <span data-ttu-id="14961-577">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14961-578">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14961-579">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-580">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-581">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14961-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14961-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14961-583">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-583">Empty</span></span> | <span data-ttu-id="14961-584">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14961-585">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14961-586">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14961-587">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14961-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14961-588">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14961-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14961-589">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14961-590">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="14961-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14961-591">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14961-592">JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14961-593">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="14961-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14961-594">Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="14961-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14961-595">Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="14961-596">Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="14961-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="14961-597">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14961-598">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="14961-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14961-599">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14961-600">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-600">MessagePack serialization options</span></span>

<span data-ttu-id="14961-601">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14961-602">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-603">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14961-604">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-604">Configure server options</span></span>

<span data-ttu-id="14961-605">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14961-606">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-606">Option</span></span> | <span data-ttu-id="14961-607">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-607">Default Value</span></span> | <span data-ttu-id="14961-608">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14961-609">30 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-609">30 seconds</span></span> | <span data-ttu-id="14961-610">Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14961-611">Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14961-612">Önerilen değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14961-613">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-613">15 seconds</span></span> | <span data-ttu-id="14961-614">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="14961-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14961-615">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-616">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-617">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-617">15 seconds</span></span> | <span data-ttu-id="14961-618">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14961-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14961-619">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="14961-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14961-620">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14961-621">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="14961-621">All installed protocols</span></span> | <span data-ttu-id="14961-622">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="14961-622">Protocols supported by this hub.</span></span> <span data-ttu-id="14961-623">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14961-624">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="14961-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14961-625">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="14961-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="14961-626">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14961-627">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14961-628">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14961-629">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="14961-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14961-630">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="14961-631">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14961-632">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-632">Option</span></span> | <span data-ttu-id="14961-633">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-633">Default Value</span></span> | <span data-ttu-id="14961-634">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14961-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-635">32 KB</span></span> | <span data-ttu-id="14961-636">Sunucu arabellekten alınan en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="14961-637">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14961-638">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="14961-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14961-639">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="14961-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14961-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-640">32 KB</span></span> | <span data-ttu-id="14961-641">Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="14961-642">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14961-643">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="14961-643">All Transports are enabled.</span></span> | <span data-ttu-id="14961-644">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="14961-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14961-645">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-645">See below.</span></span> | <span data-ttu-id="14961-646">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14961-647">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-647">See below.</span></span> | <span data-ttu-id="14961-648">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14961-649">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14961-650">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-650">Option</span></span> | <span data-ttu-id="14961-651">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-651">Default Value</span></span> | <span data-ttu-id="14961-652">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14961-653">90 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-653">90 seconds</span></span> | <span data-ttu-id="14961-654">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14961-655">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14961-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14961-656">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14961-657">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-657">Option</span></span> | <span data-ttu-id="14961-658">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-658">Default Value</span></span> | <span data-ttu-id="14961-659">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14961-660">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-660">5 seconds</span></span> | <span data-ttu-id="14961-661">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14961-662">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14961-663">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="14961-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14961-664">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-664">Configure client options</span></span>

<span data-ttu-id="14961-665">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="14961-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14961-666">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="14961-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14961-667">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-667">Configure logging</span></span>

<span data-ttu-id="14961-668">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14961-669">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14961-670">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-671">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="14961-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14961-672">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14961-673">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="14961-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14961-674">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="14961-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14961-675">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14961-676">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14961-677">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="14961-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14961-678">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="14961-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14961-679">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14961-680">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14961-681">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="14961-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14961-682">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="14961-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14961-683">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="14961-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14961-684">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14961-685">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-685">Configure allowed transports</span></span>

<span data-ttu-id="14961-686">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14961-687">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14961-688">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="14961-688">All transports are enabled by default.</span></span>

<span data-ttu-id="14961-689">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="14961-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14961-690">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14961-691">Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.</span><span class="sxs-lookup"><span data-stu-id="14961-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14961-692">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-692">Configure bearer authentication</span></span>

<span data-ttu-id="14961-693">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14961-694">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="14961-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14961-695">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="14961-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14961-696">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="14961-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14961-697">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14961-698">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="14961-699">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14961-700">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14961-701">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14961-702">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14961-703">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-704">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-705">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-705">Option</span></span> | <span data-ttu-id="14961-706">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-706">Default value</span></span> | <span data-ttu-id="14961-707">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14961-708">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-709">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-709">Timeout for server activity.</span></span> <span data-ttu-id="14961-710">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="14961-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-711">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-712">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14961-713">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-713">15 seconds</span></span> | <span data-ttu-id="14961-714">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-715">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-716">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-717">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-718">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-718">15 seconds</span></span> | <span data-ttu-id="14961-719">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-720">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-721">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14961-722">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="14961-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14961-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-724">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-724">Option</span></span> | <span data-ttu-id="14961-725">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-725">Default value</span></span> | <span data-ttu-id="14961-726">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14961-727">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-728">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-728">Timeout for server activity.</span></span> <span data-ttu-id="14961-729">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14961-730">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-731">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14961-732">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-733">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-734">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-735">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-736">Java</span><span class="sxs-lookup"><span data-stu-id="14961-736">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-737">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-737">Option</span></span> | <span data-ttu-id="14961-738">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-738">Default value</span></span> | <span data-ttu-id="14961-739">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14961-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14961-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14961-741">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-742">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-742">Timeout for server activity.</span></span> <span data-ttu-id="14961-743">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-744">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-745">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14961-746">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-746">15 seconds</span></span> | <span data-ttu-id="14961-747">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-748">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-749">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-750">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14961-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14961-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14961-752">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14961-753">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="14961-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14961-754">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="14961-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14961-755">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="14961-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14961-756">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-756">Configure additional options</span></span>

<span data-ttu-id="14961-757">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-758">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-759">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-759">.NET Option</span></span> |  <span data-ttu-id="14961-760">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-760">Default value</span></span> | <span data-ttu-id="14961-761">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14961-762">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14961-763">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-764">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-765">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14961-766">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-766">Empty</span></span> | <span data-ttu-id="14961-767">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14961-768">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-768">Empty</span></span> | <span data-ttu-id="14961-769">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14961-770">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-770">Empty</span></span> | <span data-ttu-id="14961-771">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="14961-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14961-772">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-772">5 seconds</span></span> | <span data-ttu-id="14961-773">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14961-773">WebSockets only.</span></span> <span data-ttu-id="14961-774">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14961-775">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="14961-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14961-776">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-776">Empty</span></span> | <span data-ttu-id="14961-777">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14961-778">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14961-779">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="14961-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="14961-780">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="14961-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14961-781">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="14961-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14961-782">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="14961-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14961-783">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="14961-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14961-784">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14961-785">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="14961-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14961-786">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14961-787">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="14961-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14961-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-789">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-789">JavaScript Option</span></span> | <span data-ttu-id="14961-790">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-790">Default Value</span></span> | <span data-ttu-id="14961-791">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14961-792">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14961-793">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-794">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-795">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-796">Java</span><span class="sxs-lookup"><span data-stu-id="14961-796">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-797">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-797">Java Option</span></span> | <span data-ttu-id="14961-798">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-798">Default Value</span></span> | <span data-ttu-id="14961-799">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14961-800">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14961-801">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-802">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-803">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14961-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14961-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14961-805">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-805">Empty</span></span> | <span data-ttu-id="14961-806">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14961-807">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14961-808">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14961-809">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14961-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14961-810">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14961-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14961-811">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14961-812">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="14961-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14961-813">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14961-814">JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14961-815">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="14961-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14961-816">Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="14961-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14961-817">Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="14961-818">Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="14961-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="14961-819">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14961-820">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="14961-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14961-821">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14961-822">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-822">MessagePack serialization options</span></span>

<span data-ttu-id="14961-823">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14961-824">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-825">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="14961-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14961-826">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-826">Configure server options</span></span>

<span data-ttu-id="14961-827">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14961-828">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-828">Option</span></span> | <span data-ttu-id="14961-829">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-829">Default Value</span></span> | <span data-ttu-id="14961-830">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="14961-831">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-831">15 seconds</span></span> | <span data-ttu-id="14961-832">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="14961-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14961-833">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-834">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14961-835">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-835">15 seconds</span></span> | <span data-ttu-id="14961-836">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="14961-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14961-837">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="14961-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14961-838">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14961-839">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="14961-839">All installed protocols</span></span> | <span data-ttu-id="14961-840">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="14961-840">Protocols supported by this hub.</span></span> <span data-ttu-id="14961-841">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14961-842">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="14961-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14961-843">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="14961-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="14961-844">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14961-845">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14961-846">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="14961-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14961-847">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="14961-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14961-848">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="14961-849">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="14961-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14961-850">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-850">Option</span></span> | <span data-ttu-id="14961-851">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-851">Default Value</span></span> | <span data-ttu-id="14961-852">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14961-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-853">32 KB</span></span> | <span data-ttu-id="14961-854">Sunucu arabellekten alınan en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="14961-855">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14961-856">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="14961-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14961-857">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="14961-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14961-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="14961-858">32 KB</span></span> | <span data-ttu-id="14961-859">Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="14961-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="14961-860">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14961-861">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="14961-861">All Transports are enabled.</span></span> | <span data-ttu-id="14961-862">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="14961-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14961-863">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-863">See below.</span></span> | <span data-ttu-id="14961-864">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14961-865">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-865">See below.</span></span> | <span data-ttu-id="14961-866">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="14961-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14961-867">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14961-868">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-868">Option</span></span> | <span data-ttu-id="14961-869">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-869">Default Value</span></span> | <span data-ttu-id="14961-870">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14961-871">90 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-871">90 seconds</span></span> | <span data-ttu-id="14961-872">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14961-873">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="14961-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14961-874">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="14961-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14961-875">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-875">Option</span></span> | <span data-ttu-id="14961-876">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-876">Default Value</span></span> | <span data-ttu-id="14961-877">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14961-878">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-878">5 seconds</span></span> | <span data-ttu-id="14961-879">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14961-880">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14961-881">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="14961-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14961-882">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-882">Configure client options</span></span>

<span data-ttu-id="14961-883">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="14961-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14961-884">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="14961-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14961-885">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-885">Configure logging</span></span>

<span data-ttu-id="14961-886">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="14961-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14961-887">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="14961-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14961-888">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14961-889">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="14961-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14961-890">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14961-891">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="14961-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14961-892">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="14961-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14961-893">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="14961-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14961-894">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14961-895">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="14961-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14961-896">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="14961-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14961-897">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14961-898">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="14961-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14961-899">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="14961-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14961-900">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="14961-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14961-901">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="14961-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14961-902">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14961-903">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-903">Configure allowed transports</span></span>

<span data-ttu-id="14961-904">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14961-905">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="14961-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14961-906">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="14961-906">All transports are enabled by default.</span></span>

<span data-ttu-id="14961-907">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="14961-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14961-908">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14961-909">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-909">Configure bearer authentication</span></span>

<span data-ttu-id="14961-910">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14961-911">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="14961-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14961-912">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="14961-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14961-913">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="14961-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14961-914">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14961-915">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="14961-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="14961-916">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14961-917">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="14961-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14961-918">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="14961-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14961-919">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14961-920">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-921">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-922">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-922">Option</span></span> | <span data-ttu-id="14961-923">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-923">Default value</span></span> | <span data-ttu-id="14961-924">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14961-925">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-926">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-926">Timeout for server activity.</span></span> <span data-ttu-id="14961-927">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="14961-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-928">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-929">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14961-930">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-930">15 seconds</span></span> | <span data-ttu-id="14961-931">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-932">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14961-933">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-934">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="14961-935">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="14961-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14961-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-937">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-937">Option</span></span> | <span data-ttu-id="14961-938">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-938">Default value</span></span> | <span data-ttu-id="14961-939">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14961-940">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-941">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-941">Timeout for server activity.</span></span> <span data-ttu-id="14961-942">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14961-943">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-944">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-945">Java</span><span class="sxs-lookup"><span data-stu-id="14961-945">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-946">Seçenek</span><span class="sxs-lookup"><span data-stu-id="14961-946">Option</span></span> | <span data-ttu-id="14961-947">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-947">Default value</span></span> | <span data-ttu-id="14961-948">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14961-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14961-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14961-950">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="14961-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14961-951">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="14961-951">Timeout for server activity.</span></span> <span data-ttu-id="14961-952">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-953">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14961-954">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="14961-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14961-955">15 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-955">15 seconds</span></span> | <span data-ttu-id="14961-956">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="14961-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="14961-957">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="14961-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14961-958">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="14961-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14961-959">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="14961-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14961-960">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="14961-960">Configure additional options</span></span>

<span data-ttu-id="14961-961">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14961-962">.NET</span><span class="sxs-lookup"><span data-stu-id="14961-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14961-963">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-963">.NET Option</span></span> |  <span data-ttu-id="14961-964">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="14961-964">Default value</span></span> | <span data-ttu-id="14961-965">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14961-966">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14961-967">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-968">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-969">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14961-970">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-970">Empty</span></span> | <span data-ttu-id="14961-971">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14961-972">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-972">Empty</span></span> | <span data-ttu-id="14961-973">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="14961-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14961-974">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-974">Empty</span></span> | <span data-ttu-id="14961-975">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="14961-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14961-976">5 saniye</span><span class="sxs-lookup"><span data-stu-id="14961-976">5 seconds</span></span> | <span data-ttu-id="14961-977">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14961-977">WebSockets only.</span></span> <span data-ttu-id="14961-978">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="14961-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14961-979">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="14961-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14961-980">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-980">Empty</span></span> | <span data-ttu-id="14961-981">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14961-982">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14961-983">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="14961-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="14961-984">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="14961-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14961-985">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="14961-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14961-986">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="14961-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14961-987">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="14961-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14961-988">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14961-989">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="14961-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14961-990">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="14961-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14961-991">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="14961-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14961-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14961-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14961-993">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-993">JavaScript Option</span></span> | <span data-ttu-id="14961-994">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-994">Default Value</span></span> | <span data-ttu-id="14961-995">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14961-996">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14961-997">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-998">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-999">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14961-1000">Java</span><span class="sxs-lookup"><span data-stu-id="14961-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="14961-1001">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="14961-1001">Java Option</span></span> | <span data-ttu-id="14961-1002">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="14961-1002">Default Value</span></span> | <span data-ttu-id="14961-1003">Açıklama</span><span class="sxs-lookup"><span data-stu-id="14961-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14961-1004">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="14961-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14961-1005">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="14961-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14961-1006">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="14961-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14961-1007">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="14961-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14961-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14961-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14961-1009">Boş</span><span class="sxs-lookup"><span data-stu-id="14961-1009">Empty</span></span> | <span data-ttu-id="14961-1010">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="14961-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14961-1011">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="14961-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14961-1012">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="14961-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14961-1013">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14961-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14961-1014">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="14961-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
