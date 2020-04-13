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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223991"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="16b53-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="16b53-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16b53-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16b53-105">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="16b53-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16b53-106">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16b53-107">JSON [serileştirme, AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="16b53-108">`AddJsonProtocol`[addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) sonra `Startup.ConfigureServices`eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16b53-109">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16b53-110">Bu nesnedeki [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="16b53-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16b53-111">Daha fazla bilgi için [System.Text.Json belgelerine](/dotnet/api/system.text.json)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="16b53-112">Örnek olarak, varsayılan "camelCase" adları yerine özellik adlarının kasasını değiştirmemek için serileştiriciyi yapılandırmak `Startup.ConfigureServices`için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="16b53-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="16b53-113">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16b53-114">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="16b53-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16b53-115">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="16b53-116">Newtonsoft.Json'a geç</span><span class="sxs-lookup"><span data-stu-id="16b53-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="16b53-117">Eğer bu özellikleri `Newtonsoft.Json` desteklenmez `System.Text.Json`gerekiyorsa, [Newtonsoft.Json geçiş](xref:migration/22-to-30#switch-to-newtonsoftjson)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16b53-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-118">MessagePack serialization options</span></span>

<span data-ttu-id="16b53-119">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16b53-120">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-121">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16b53-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-122">Configure server options</span></span>

<span data-ttu-id="16b53-123">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16b53-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-124">Option</span></span> | <span data-ttu-id="16b53-125">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-125">Default Value</span></span> | <span data-ttu-id="16b53-126">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16b53-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-127">30 seconds</span></span> | <span data-ttu-id="16b53-128">Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16b53-129">Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16b53-130">Önerilen değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16b53-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-131">15 seconds</span></span> | <span data-ttu-id="16b53-132">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16b53-133">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-134">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16b53-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-135">15 seconds</span></span> | <span data-ttu-id="16b53-136">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16b53-137">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="16b53-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16b53-138">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16b53-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="16b53-139">All installed protocols</span></span> | <span data-ttu-id="16b53-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="16b53-140">Protocols supported by this hub.</span></span> <span data-ttu-id="16b53-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16b53-142">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="16b53-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16b53-143">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="16b53-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="16b53-144">İstemci yükleme akışları için arabelleğe alınabilecek maksimum öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="16b53-145">Bu sınıra ulaşılırsa, sunucu öğeleri akış işlemlerine kadar çağrıların işlenmesi engellenir.</span><span class="sxs-lookup"><span data-stu-id="16b53-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="16b53-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-146">32 KB</span></span> | <span data-ttu-id="16b53-147">Tek bir gelen hub iletisinin maksimum boyutu.</span><span class="sxs-lookup"><span data-stu-id="16b53-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="16b53-148">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16b53-149">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16b53-150">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16b53-151">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="16b53-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16b53-152">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16b53-153">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16b53-154">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-154">Option</span></span> | <span data-ttu-id="16b53-155">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-155">Default Value</span></span> | <span data-ttu-id="16b53-156">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16b53-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-157">32 KB</span></span> | <span data-ttu-id="16b53-158">Arka basınç uygulamadan önce sunucunun arabellek yaptığı istemciden alınan maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="16b53-159">Bu değeri artırmak, sunucunun geri baskı uygulamadan daha büyük iletileri daha hızlı almasını sağlar, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16b53-160">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="16b53-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16b53-161">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="16b53-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16b53-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-162">32 KB</span></span> | <span data-ttu-id="16b53-163">Arka basıncı gözlemlemeden önce sunucunun arabellek yaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="16b53-164">Bu değeri artırmak, sunucunun daha büyük iletileri geri baskı beklemeden daha hızlı arabelleğe almamasına olanak tanır, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16b53-165">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="16b53-165">All Transports are enabled.</span></span> | <span data-ttu-id="16b53-166">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16b53-167">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-167">See below.</span></span> | <span data-ttu-id="16b53-168">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16b53-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-169">See below.</span></span> | <span data-ttu-id="16b53-170">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16b53-171">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16b53-172">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-172">Option</span></span> | <span data-ttu-id="16b53-173">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-173">Default Value</span></span> | <span data-ttu-id="16b53-174">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16b53-175">90 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-175">90 seconds</span></span> | <span data-ttu-id="16b53-176">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16b53-177">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="16b53-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16b53-178">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16b53-179">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-179">Option</span></span> | <span data-ttu-id="16b53-180">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-180">Default Value</span></span> | <span data-ttu-id="16b53-181">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16b53-182">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-182">5 seconds</span></span> | <span data-ttu-id="16b53-183">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16b53-184">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16b53-185">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="16b53-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16b53-186">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-186">Configure client options</span></span>

<span data-ttu-id="16b53-187">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="16b53-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16b53-188">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="16b53-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16b53-189">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-189">Configure logging</span></span>

<span data-ttu-id="16b53-190">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16b53-191">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16b53-192">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-193">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="16b53-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16b53-194">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16b53-195">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="16b53-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16b53-196">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="16b53-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16b53-197">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16b53-198">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16b53-199">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="16b53-200">Bir `LogLevel` değer yerine, günlük düzeyi `string` adını temsil eden bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="16b53-201">Bu, `LogLevel` sabitlere erişiminiz olmayan ortamlarda SignalR günlüğe kaydetmeyi yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="16b53-202">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="16b53-202">The following table lists the available log levels.</span></span> <span data-ttu-id="16b53-203">Günlüğe kaydedilecek `configureLogging` **minimum** günlük düzeyini ayarlar için sağladığınız değer.</span><span class="sxs-lookup"><span data-stu-id="16b53-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="16b53-204">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="16b53-205">Dize</span><span class="sxs-lookup"><span data-stu-id="16b53-205">String</span></span>                      | <span data-ttu-id="16b53-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="16b53-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="16b53-207">`info`**veya**`information`</span><span class="sxs-lookup"><span data-stu-id="16b53-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="16b53-208">`warn`**veya**`warning`</span><span class="sxs-lookup"><span data-stu-id="16b53-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="16b53-209">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="16b53-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16b53-210">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16b53-211">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16b53-212">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="16b53-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16b53-213">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="16b53-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16b53-214">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="16b53-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16b53-215">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16b53-216">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-216">Configure allowed transports</span></span>

<span data-ttu-id="16b53-217">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16b53-218">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16b53-219">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-219">All transports are enabled by default.</span></span>

<span data-ttu-id="16b53-220">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="16b53-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16b53-221">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16b53-222">Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.</span><span class="sxs-lookup"><span data-stu-id="16b53-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="16b53-223">Java istemcisinde, aktarım `withTransport` `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="16b53-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="16b53-224">Java istemcisi varsayılan olarak WebSockets aktarımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16b53-225">SignalR Java istemcisi henüz geri dönüş taşıma yı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16b53-226">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-226">Configure bearer authentication</span></span>

<span data-ttu-id="16b53-227">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16b53-228">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="16b53-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16b53-229">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16b53-230">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16b53-231">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16b53-232">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16b53-233">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16b53-234">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16b53-235">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16b53-236">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16b53-237">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-238">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-239">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-239">Option</span></span> | <span data-ttu-id="16b53-240">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-240">Default value</span></span> | <span data-ttu-id="16b53-241">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16b53-242">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-243">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-243">Timeout for server activity.</span></span> <span data-ttu-id="16b53-244">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="16b53-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-245">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-246">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16b53-247">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-247">15 seconds</span></span> | <span data-ttu-id="16b53-248">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-249">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-250">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-251">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16b53-252">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-252">15 seconds</span></span> | <span data-ttu-id="16b53-253">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-254">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-255">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16b53-256">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16b53-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-258">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-258">Option</span></span> | <span data-ttu-id="16b53-259">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-259">Default value</span></span> | <span data-ttu-id="16b53-260">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16b53-261">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-262">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-262">Timeout for server activity.</span></span> <span data-ttu-id="16b53-263">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16b53-264">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-265">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16b53-266">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16b53-267">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-268">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-269">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-270">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-270">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-271">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-271">Option</span></span> | <span data-ttu-id="16b53-272">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-272">Default value</span></span> | <span data-ttu-id="16b53-273">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16b53-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16b53-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16b53-275">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-276">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-276">Timeout for server activity.</span></span> <span data-ttu-id="16b53-277">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-278">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-279">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16b53-280">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-280">15 seconds</span></span> | <span data-ttu-id="16b53-281">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-282">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-283">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-284">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16b53-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16b53-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16b53-286">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16b53-287">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-288">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-289">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16b53-290">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-290">Configure additional options</span></span>

<span data-ttu-id="16b53-291">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-292">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-293">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-293">.NET Option</span></span> |  <span data-ttu-id="16b53-294">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-294">Default value</span></span> | <span data-ttu-id="16b53-295">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16b53-296">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16b53-297">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-298">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-299">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16b53-300">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-300">Empty</span></span> | <span data-ttu-id="16b53-301">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16b53-302">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-302">Empty</span></span> | <span data-ttu-id="16b53-303">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16b53-304">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-304">Empty</span></span> | <span data-ttu-id="16b53-305">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="16b53-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16b53-306">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-306">5 seconds</span></span> | <span data-ttu-id="16b53-307">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="16b53-307">WebSockets only.</span></span> <span data-ttu-id="16b53-308">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16b53-309">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="16b53-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16b53-310">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-310">Empty</span></span> | <span data-ttu-id="16b53-311">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16b53-312">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16b53-313">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="16b53-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="16b53-314">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16b53-315">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="16b53-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16b53-316">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="16b53-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16b53-317">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="16b53-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16b53-318">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16b53-319">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16b53-320">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16b53-321">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16b53-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-323">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-323">JavaScript Option</span></span> | <span data-ttu-id="16b53-324">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-324">Default Value</span></span> | <span data-ttu-id="16b53-325">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16b53-326">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16b53-327">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-328">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-329">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-330">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-330">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-331">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-331">Java Option</span></span> | <span data-ttu-id="16b53-332">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-332">Default Value</span></span> | <span data-ttu-id="16b53-333">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16b53-334">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16b53-335">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-336">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-337">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16b53-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16b53-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16b53-339">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-339">Empty</span></span> | <span data-ttu-id="16b53-340">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16b53-341">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16b53-342">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16b53-343">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16b53-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16b53-344">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="16b53-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16b53-345">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16b53-346">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="16b53-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16b53-347">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16b53-348">JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16b53-349">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16b53-350">Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="16b53-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16b53-351">Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="16b53-352">Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="16b53-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="16b53-353">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16b53-354">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="16b53-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16b53-355">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16b53-356">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-356">MessagePack serialization options</span></span>

<span data-ttu-id="16b53-357">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16b53-358">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-359">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16b53-360">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-360">Configure server options</span></span>

<span data-ttu-id="16b53-361">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16b53-362">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-362">Option</span></span> | <span data-ttu-id="16b53-363">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-363">Default Value</span></span> | <span data-ttu-id="16b53-364">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16b53-365">30 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-365">30 seconds</span></span> | <span data-ttu-id="16b53-366">Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16b53-367">Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16b53-368">Önerilen değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16b53-369">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-369">15 seconds</span></span> | <span data-ttu-id="16b53-370">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16b53-371">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-372">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16b53-373">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-373">15 seconds</span></span> | <span data-ttu-id="16b53-374">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16b53-375">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="16b53-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16b53-376">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16b53-377">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="16b53-377">All installed protocols</span></span> | <span data-ttu-id="16b53-378">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="16b53-378">Protocols supported by this hub.</span></span> <span data-ttu-id="16b53-379">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16b53-380">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="16b53-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16b53-381">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="16b53-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="16b53-382">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16b53-383">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16b53-384">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16b53-385">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="16b53-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16b53-386">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16b53-387">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16b53-388">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-388">Option</span></span> | <span data-ttu-id="16b53-389">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-389">Default Value</span></span> | <span data-ttu-id="16b53-390">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16b53-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-391">32 KB</span></span> | <span data-ttu-id="16b53-392">Sunucu arabellekten alınan en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="16b53-393">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16b53-394">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="16b53-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16b53-395">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="16b53-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16b53-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-396">32 KB</span></span> | <span data-ttu-id="16b53-397">Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="16b53-398">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16b53-399">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="16b53-399">All Transports are enabled.</span></span> | <span data-ttu-id="16b53-400">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16b53-401">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-401">See below.</span></span> | <span data-ttu-id="16b53-402">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16b53-403">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-403">See below.</span></span> | <span data-ttu-id="16b53-404">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16b53-405">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16b53-406">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-406">Option</span></span> | <span data-ttu-id="16b53-407">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-407">Default Value</span></span> | <span data-ttu-id="16b53-408">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16b53-409">90 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-409">90 seconds</span></span> | <span data-ttu-id="16b53-410">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16b53-411">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="16b53-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16b53-412">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16b53-413">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-413">Option</span></span> | <span data-ttu-id="16b53-414">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-414">Default Value</span></span> | <span data-ttu-id="16b53-415">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16b53-416">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-416">5 seconds</span></span> | <span data-ttu-id="16b53-417">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16b53-418">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16b53-419">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="16b53-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16b53-420">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-420">Configure client options</span></span>

<span data-ttu-id="16b53-421">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="16b53-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16b53-422">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="16b53-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16b53-423">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-423">Configure logging</span></span>

<span data-ttu-id="16b53-424">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16b53-425">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16b53-426">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-427">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="16b53-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16b53-428">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16b53-429">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="16b53-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16b53-430">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="16b53-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16b53-431">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16b53-432">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16b53-433">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16b53-434">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="16b53-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16b53-435">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16b53-436">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16b53-437">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="16b53-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16b53-438">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="16b53-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16b53-439">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="16b53-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16b53-440">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16b53-441">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-441">Configure allowed transports</span></span>

<span data-ttu-id="16b53-442">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16b53-443">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16b53-444">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-444">All transports are enabled by default.</span></span>

<span data-ttu-id="16b53-445">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="16b53-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16b53-446">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16b53-447">Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.</span><span class="sxs-lookup"><span data-stu-id="16b53-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16b53-448">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-448">Configure bearer authentication</span></span>

<span data-ttu-id="16b53-449">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16b53-450">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="16b53-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16b53-451">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16b53-452">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16b53-453">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16b53-454">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16b53-455">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16b53-456">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16b53-457">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16b53-458">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16b53-459">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-460">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-461">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-461">Option</span></span> | <span data-ttu-id="16b53-462">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-462">Default value</span></span> | <span data-ttu-id="16b53-463">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16b53-464">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-465">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-465">Timeout for server activity.</span></span> <span data-ttu-id="16b53-466">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="16b53-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-467">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-468">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16b53-469">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-469">15 seconds</span></span> | <span data-ttu-id="16b53-470">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-471">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-472">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-473">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16b53-474">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-474">15 seconds</span></span> | <span data-ttu-id="16b53-475">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-476">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-477">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16b53-478">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16b53-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-480">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-480">Option</span></span> | <span data-ttu-id="16b53-481">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-481">Default value</span></span> | <span data-ttu-id="16b53-482">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16b53-483">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-484">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-484">Timeout for server activity.</span></span> <span data-ttu-id="16b53-485">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16b53-486">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-487">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16b53-488">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16b53-489">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-490">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-491">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-492">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-492">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-493">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-493">Option</span></span> | <span data-ttu-id="16b53-494">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-494">Default value</span></span> | <span data-ttu-id="16b53-495">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16b53-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16b53-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16b53-497">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-498">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-498">Timeout for server activity.</span></span> <span data-ttu-id="16b53-499">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-500">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-501">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16b53-502">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-502">15 seconds</span></span> | <span data-ttu-id="16b53-503">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-504">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-505">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-506">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16b53-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16b53-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16b53-508">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16b53-509">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="16b53-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16b53-510">İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16b53-511">İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16b53-512">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-512">Configure additional options</span></span>

<span data-ttu-id="16b53-513">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-514">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-515">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-515">.NET Option</span></span> |  <span data-ttu-id="16b53-516">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-516">Default value</span></span> | <span data-ttu-id="16b53-517">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16b53-518">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16b53-519">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-520">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-521">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16b53-522">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-522">Empty</span></span> | <span data-ttu-id="16b53-523">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16b53-524">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-524">Empty</span></span> | <span data-ttu-id="16b53-525">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16b53-526">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-526">Empty</span></span> | <span data-ttu-id="16b53-527">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="16b53-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16b53-528">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-528">5 seconds</span></span> | <span data-ttu-id="16b53-529">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="16b53-529">WebSockets only.</span></span> <span data-ttu-id="16b53-530">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16b53-531">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="16b53-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16b53-532">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-532">Empty</span></span> | <span data-ttu-id="16b53-533">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16b53-534">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16b53-535">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="16b53-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="16b53-536">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16b53-537">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="16b53-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16b53-538">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="16b53-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16b53-539">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="16b53-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16b53-540">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16b53-541">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16b53-542">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16b53-543">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16b53-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-545">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-545">JavaScript Option</span></span> | <span data-ttu-id="16b53-546">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-546">Default Value</span></span> | <span data-ttu-id="16b53-547">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16b53-548">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16b53-549">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-550">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-551">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-552">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-552">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-553">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-553">Java Option</span></span> | <span data-ttu-id="16b53-554">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-554">Default Value</span></span> | <span data-ttu-id="16b53-555">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16b53-556">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16b53-557">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-558">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-559">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16b53-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16b53-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16b53-561">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-561">Empty</span></span> | <span data-ttu-id="16b53-562">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16b53-563">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16b53-564">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16b53-565">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16b53-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16b53-566">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="16b53-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16b53-567">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16b53-568">ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="16b53-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16b53-569">Her protokolün serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16b53-570">JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16b53-571">Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16b53-572">Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="16b53-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16b53-573">Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="16b53-574">Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="16b53-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="16b53-575">.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16b53-576">Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:</span><span class="sxs-lookup"><span data-stu-id="16b53-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16b53-577">Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16b53-578">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-578">MessagePack serialization options</span></span>

<span data-ttu-id="16b53-579">MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16b53-580">Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-581">Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="16b53-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16b53-582">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-582">Configure server options</span></span>

<span data-ttu-id="16b53-583">Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16b53-584">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-584">Option</span></span> | <span data-ttu-id="16b53-585">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-585">Default Value</span></span> | <span data-ttu-id="16b53-586">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="16b53-587">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-587">15 seconds</span></span> | <span data-ttu-id="16b53-588">İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16b53-589">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-590">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16b53-591">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-591">15 seconds</span></span> | <span data-ttu-id="16b53-592">Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16b53-593">Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="16b53-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16b53-594">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16b53-595">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="16b53-595">All installed protocols</span></span> | <span data-ttu-id="16b53-596">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="16b53-596">Protocols supported by this hub.</span></span> <span data-ttu-id="16b53-597">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16b53-598">Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse.</span><span class="sxs-lookup"><span data-stu-id="16b53-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16b53-599">Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi.</span><span class="sxs-lookup"><span data-stu-id="16b53-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="16b53-600">Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16b53-601">Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16b53-602">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="16b53-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16b53-603">Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions`</span><span class="sxs-lookup"><span data-stu-id="16b53-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16b53-604">Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16b53-605">Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="16b53-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16b53-606">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-606">Option</span></span> | <span data-ttu-id="16b53-607">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-607">Default Value</span></span> | <span data-ttu-id="16b53-608">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16b53-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-609">32 KB</span></span> | <span data-ttu-id="16b53-610">Sunucu arabellekten alınan en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="16b53-611">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16b53-612">Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler.</span><span class="sxs-lookup"><span data-stu-id="16b53-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16b53-613">İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="16b53-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16b53-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="16b53-614">32 KB</span></span> | <span data-ttu-id="16b53-615">Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="16b53-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="16b53-616">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16b53-617">Tüm Taşımalar etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="16b53-617">All Transports are enabled.</span></span> | <span data-ttu-id="16b53-618">Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder.</span><span class="sxs-lookup"><span data-stu-id="16b53-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16b53-619">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-619">See below.</span></span> | <span data-ttu-id="16b53-620">Uzun Yoklama taşımasına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16b53-621">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-621">See below.</span></span> | <span data-ttu-id="16b53-622">WebSockets aktarımına özel ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="16b53-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16b53-623">Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16b53-624">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-624">Option</span></span> | <span data-ttu-id="16b53-625">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-625">Default Value</span></span> | <span data-ttu-id="16b53-626">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16b53-627">90 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-627">90 seconds</span></span> | <span data-ttu-id="16b53-628">Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16b53-629">Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="16b53-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16b53-630">WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="16b53-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16b53-631">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-631">Option</span></span> | <span data-ttu-id="16b53-632">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-632">Default Value</span></span> | <span data-ttu-id="16b53-633">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16b53-634">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-634">5 seconds</span></span> | <span data-ttu-id="16b53-635">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16b53-636">Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16b53-637">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="16b53-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16b53-638">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-638">Configure client options</span></span>

<span data-ttu-id="16b53-639">İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="16b53-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16b53-640">Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.</span><span class="sxs-lookup"><span data-stu-id="16b53-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16b53-641">Günlük işlemlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-641">Configure logging</span></span>

<span data-ttu-id="16b53-642">Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16b53-643">Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16b53-644">Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16b53-645">Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="16b53-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16b53-646">Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16b53-647">Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="16b53-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16b53-648">Uzantı `AddConsole` yöntemini arayın:</span><span class="sxs-lookup"><span data-stu-id="16b53-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16b53-649">JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16b53-650">Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16b53-651">Günlükler tarayıcı konsolpenceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16b53-652">Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.</span><span class="sxs-lookup"><span data-stu-id="16b53-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16b53-653">Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16b53-654">SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16b53-655">Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir.</span><span class="sxs-lookup"><span data-stu-id="16b53-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16b53-656">Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="16b53-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16b53-657">Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="16b53-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16b53-658">Bu güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16b53-659">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-659">Configure allowed transports</span></span>

<span data-ttu-id="16b53-660">SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16b53-661">İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16b53-662">Tüm aktarımlar varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-662">All transports are enabled by default.</span></span>

<span data-ttu-id="16b53-663">Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="16b53-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16b53-664">JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16b53-665">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-665">Configure bearer authentication</span></span>

<span data-ttu-id="16b53-666">SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16b53-667">.NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması).</span><span class="sxs-lookup"><span data-stu-id="16b53-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16b53-668">JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="16b53-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16b53-669">Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="16b53-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16b53-670">.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16b53-671">JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="16b53-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16b53-672">SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16b53-673">[Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="16b53-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16b53-674">[Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="16b53-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16b53-675">Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16b53-676">Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-677">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-678">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-678">Option</span></span> | <span data-ttu-id="16b53-679">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-679">Default value</span></span> | <span data-ttu-id="16b53-680">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16b53-681">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-682">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-682">Timeout for server activity.</span></span> <span data-ttu-id="16b53-683">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose`</span><span class="sxs-lookup"><span data-stu-id="16b53-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-684">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-685">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16b53-686">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-686">15 seconds</span></span> | <span data-ttu-id="16b53-687">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-688">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16b53-689">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-690">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="16b53-691">.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="16b53-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16b53-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-693">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-693">Option</span></span> | <span data-ttu-id="16b53-694">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-694">Default value</span></span> | <span data-ttu-id="16b53-695">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16b53-696">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-697">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-697">Timeout for server activity.</span></span> <span data-ttu-id="16b53-698">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16b53-699">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-700">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-701">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-701">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-702">Seçenek</span><span class="sxs-lookup"><span data-stu-id="16b53-702">Option</span></span> | <span data-ttu-id="16b53-703">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-703">Default value</span></span> | <span data-ttu-id="16b53-704">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16b53-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16b53-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16b53-706">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="16b53-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16b53-707">Sunucu etkinliği için zaman arası.</span><span class="sxs-lookup"><span data-stu-id="16b53-707">Timeout for server activity.</span></span> <span data-ttu-id="16b53-708">Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-709">Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16b53-710">Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="16b53-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16b53-711">15 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-711">15 seconds</span></span> | <span data-ttu-id="16b53-712">İlk sunucu el sıkışması için zaman ara.</span><span class="sxs-lookup"><span data-stu-id="16b53-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="16b53-713">Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="16b53-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16b53-714">Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="16b53-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16b53-715">El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="16b53-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16b53-716">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="16b53-716">Configure additional options</span></span>

<span data-ttu-id="16b53-717">Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16b53-718">.NET</span><span class="sxs-lookup"><span data-stu-id="16b53-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16b53-719">.NET Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-719">.NET Option</span></span> |  <span data-ttu-id="16b53-720">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="16b53-720">Default value</span></span> | <span data-ttu-id="16b53-721">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16b53-722">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16b53-723">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-724">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-725">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16b53-726">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-726">Empty</span></span> | <span data-ttu-id="16b53-727">İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16b53-728">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-728">Empty</span></span> | <span data-ttu-id="16b53-729">Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="16b53-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16b53-730">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-730">Empty</span></span> | <span data-ttu-id="16b53-731">Her HTTP isteğiyle gönderilecek kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="16b53-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16b53-732">5 saniye</span><span class="sxs-lookup"><span data-stu-id="16b53-732">5 seconds</span></span> | <span data-ttu-id="16b53-733">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="16b53-733">WebSockets only.</span></span> <span data-ttu-id="16b53-734">İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="16b53-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16b53-735">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="16b53-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16b53-736">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-736">Empty</span></span> | <span data-ttu-id="16b53-737">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16b53-738">HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16b53-739">WebSocket bağlantıları için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="16b53-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="16b53-740">Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16b53-741">Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün.</span><span class="sxs-lookup"><span data-stu-id="16b53-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16b53-742">**İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.**</span><span class="sxs-lookup"><span data-stu-id="16b53-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16b53-743">HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si.</span><span class="sxs-lookup"><span data-stu-id="16b53-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16b53-744">BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16b53-745">Bu, Windows kimlik doğrulamasının kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="16b53-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16b53-746">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="16b53-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16b53-747">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="16b53-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16b53-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16b53-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16b53-749">JavaScript Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-749">JavaScript Option</span></span> | <span data-ttu-id="16b53-750">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-750">Default Value</span></span> | <span data-ttu-id="16b53-751">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16b53-752">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16b53-753">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-754">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-755">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16b53-756">Java</span><span class="sxs-lookup"><span data-stu-id="16b53-756">Java</span></span>](#tab/java)

| <span data-ttu-id="16b53-757">Java Seçeneği</span><span class="sxs-lookup"><span data-stu-id="16b53-757">Java Option</span></span> | <span data-ttu-id="16b53-758">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="16b53-758">Default Value</span></span> | <span data-ttu-id="16b53-759">Açıklama</span><span class="sxs-lookup"><span data-stu-id="16b53-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16b53-760">HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="16b53-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16b53-761">Bunu, `true` anlaşma adımını atlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="16b53-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16b53-762">**Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.**</span><span class="sxs-lookup"><span data-stu-id="16b53-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16b53-763">Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="16b53-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16b53-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16b53-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16b53-765">Boş</span><span class="sxs-lookup"><span data-stu-id="16b53-765">Empty</span></span> | <span data-ttu-id="16b53-766">Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita.</span><span class="sxs-lookup"><span data-stu-id="16b53-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16b53-767">.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16b53-768">JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="16b53-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16b53-769">Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16b53-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16b53-770">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="16b53-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
