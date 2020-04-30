---
title: ASP.NET Core SignalR yapılandırması
author: bradygaster
description: ASP.NET Core SignalR uygulamalarını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559004"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9dcdb-103">ASP.NET Core SignalR yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9dcdb-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9dcdb-104">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-105">ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9dcdb-106">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9dcdb-107">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9dcdb-108">`AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9dcdb-109">Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9dcdb-110">Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9dcdb-111">Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9dcdb-112">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9dcdb-113">.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9dcdb-114">Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9dcdb-115">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9dcdb-116">Newtonsoft. JSON öğesine geç</span><span class="sxs-lookup"><span data-stu-id="9dcdb-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9dcdb-117">' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9dcdb-118">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-118">MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-119">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9dcdb-120">Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9dcdb-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-121">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9dcdb-122">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-122">Configure server options</span></span>

<span data-ttu-id="9dcdb-123">Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9dcdb-124">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-124">Option</span></span> | <span data-ttu-id="9dcdb-125">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-125">Default Value</span></span> | <span data-ttu-id="9dcdb-126">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9dcdb-127">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-127">30 seconds</span></span> | <span data-ttu-id="9dcdb-128">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9dcdb-129">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9dcdb-130">Önerilen değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-131">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-131">15 seconds</span></span> | <span data-ttu-id="9dcdb-132">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9dcdb-133">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-134">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-135">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-135">15 seconds</span></span> | <span data-ttu-id="9dcdb-136">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9dcdb-137">Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9dcdb-138">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9dcdb-139">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9dcdb-139">All installed protocols</span></span> | <span data-ttu-id="9dcdb-140">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9dcdb-141">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9dcdb-142">İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9dcdb-143">Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9dcdb-144">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9dcdb-145">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9dcdb-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-146">32 KB</span></span> | <span data-ttu-id="9dcdb-147">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9dcdb-148">Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9dcdb-149">Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9dcdb-150">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9dcdb-151">Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9dcdb-152">Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9dcdb-153">Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9dcdb-154">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-154">Option</span></span> | <span data-ttu-id="9dcdb-155">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-155">Default Value</span></span> | <span data-ttu-id="9dcdb-156">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9dcdb-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-157">32 KB</span></span> | <span data-ttu-id="9dcdb-158">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9dcdb-159">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9dcdb-160">Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9dcdb-161">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9dcdb-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-162">32 KB</span></span> | <span data-ttu-id="9dcdb-163">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9dcdb-164">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9dcdb-165">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-165">All Transports are enabled.</span></span> | <span data-ttu-id="9dcdb-166">Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9dcdb-167">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-167">See below.</span></span> | <span data-ttu-id="9dcdb-168">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9dcdb-169">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-169">See below.</span></span> | <span data-ttu-id="9dcdb-170">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9dcdb-171">0</span><span class="sxs-lookup"><span data-stu-id="9dcdb-171">0</span></span> | <span data-ttu-id="9dcdb-172">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9dcdb-173">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9dcdb-174">Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9dcdb-175">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-175">Option</span></span> | <span data-ttu-id="9dcdb-176">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-176">Default Value</span></span> | <span data-ttu-id="9dcdb-177">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9dcdb-178">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-178">90 seconds</span></span> | <span data-ttu-id="9dcdb-179">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9dcdb-180">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9dcdb-181">WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9dcdb-182">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-182">Option</span></span> | <span data-ttu-id="9dcdb-183">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-183">Default Value</span></span> | <span data-ttu-id="9dcdb-184">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9dcdb-185">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-185">5 seconds</span></span> | <span data-ttu-id="9dcdb-186">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9dcdb-187">`Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9dcdb-188">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9dcdb-189">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-189">Configure client options</span></span>

<span data-ttu-id="9dcdb-190">İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9dcdb-191">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9dcdb-192">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-192">Configure logging</span></span>

<span data-ttu-id="9dcdb-193">Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9dcdb-194">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9dcdb-195">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-196">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9dcdb-197">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9dcdb-198">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9dcdb-199">`AddConsole` Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9dcdb-200">JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9dcdb-201">Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9dcdb-202">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9dcdb-203">Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9dcdb-204">Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9dcdb-205">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-205">The following table lists the available log levels.</span></span> <span data-ttu-id="9dcdb-206">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9dcdb-207">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9dcdb-208">Dize</span><span class="sxs-lookup"><span data-stu-id="9dcdb-208">String</span></span>                      | <span data-ttu-id="9dcdb-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9dcdb-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9dcdb-210">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9dcdb-211">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9dcdb-212">Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9dcdb-213">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9dcdb-214">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9dcdb-215">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9dcdb-216">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9dcdb-217">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9dcdb-218">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9dcdb-219">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-219">Configure allowed transports</span></span>

<span data-ttu-id="9dcdb-220">SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9dcdb-221">Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9dcdb-222">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-222">All transports are enabled by default.</span></span>

<span data-ttu-id="9dcdb-223">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9dcdb-224">JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9dcdb-225">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9dcdb-226">Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9dcdb-227">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9dcdb-228">SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9dcdb-229">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-229">Configure bearer authentication</span></span>

<span data-ttu-id="9dcdb-230">Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9dcdb-231">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9dcdb-232">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9dcdb-233">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9dcdb-234">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9dcdb-235">JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9dcdb-236">SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9dcdb-237">[Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9dcdb-238">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9dcdb-239">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9dcdb-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9dcdb-240">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-241">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-242">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-242">Option</span></span> | <span data-ttu-id="9dcdb-243">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-243">Default value</span></span> | <span data-ttu-id="9dcdb-244">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9dcdb-245">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-246">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-246">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-247">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-248">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-249">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-250">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-250">15 seconds</span></span> | <span data-ttu-id="9dcdb-251">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-252">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-253">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-254">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-255">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-255">15 seconds</span></span> | <span data-ttu-id="9dcdb-256">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-257">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-258">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9dcdb-259">.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-261">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-261">Option</span></span> | <span data-ttu-id="9dcdb-262">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-262">Default value</span></span> | <span data-ttu-id="9dcdb-263">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9dcdb-264">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-265">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-265">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-266">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9dcdb-267">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-268">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9dcdb-269">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-270">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-271">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-272">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-273">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-273">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-274">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-274">Option</span></span> | <span data-ttu-id="9dcdb-275">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-275">Default value</span></span> | <span data-ttu-id="9dcdb-276">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9dcdb-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9dcdb-278">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-279">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-279">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-280">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-281">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-282">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9dcdb-283">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-283">15 seconds</span></span> | <span data-ttu-id="9dcdb-284">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-285">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-286">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-287">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9dcdb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9dcdb-289">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-290">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-291">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-292">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9dcdb-293">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-293">Configure additional options</span></span>

<span data-ttu-id="9dcdb-294">Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-295">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-296">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-296">.NET Option</span></span> |  <span data-ttu-id="9dcdb-297">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-297">Default value</span></span> | <span data-ttu-id="9dcdb-298">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-299">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9dcdb-300">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-301">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-302">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9dcdb-303">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-303">Empty</span></span> | <span data-ttu-id="9dcdb-304">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9dcdb-305">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-305">Empty</span></span> | <span data-ttu-id="9dcdb-306">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9dcdb-307">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-307">Empty</span></span> | <span data-ttu-id="9dcdb-308">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9dcdb-309">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-309">5 seconds</span></span> | <span data-ttu-id="9dcdb-310">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-310">WebSockets only.</span></span> <span data-ttu-id="9dcdb-311">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9dcdb-312">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9dcdb-313">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-313">Empty</span></span> | <span data-ttu-id="9dcdb-314">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9dcdb-315">HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9dcdb-316">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="9dcdb-317">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9dcdb-318">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9dcdb-319">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9dcdb-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9dcdb-320">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9dcdb-321">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9dcdb-322">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9dcdb-323">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9dcdb-324">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-326">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-326">JavaScript Option</span></span> | <span data-ttu-id="9dcdb-327">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-327">Default Value</span></span> | <span data-ttu-id="9dcdb-328">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9dcdb-329">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9dcdb-330">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-331">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-332">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="9dcdb-333">CORS isteğiyle kimlik bilgilerinin gönderilip gönderilmeyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="9dcdb-334">Azure App Service, yapışkan oturumlar için tanımlama bilgilerini kullanır ve bu seçeneğin doğru şekilde çalışmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="9dcdb-335">SignalR ile CORS hakkında daha fazla bilgi için bkz <xref:signalr/security#cross-origin-resource-sharing>..</span><span class="sxs-lookup"><span data-stu-id="9dcdb-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-336">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-336">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-337">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-337">Java Option</span></span> | <span data-ttu-id="9dcdb-338">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-338">Default Value</span></span> | <span data-ttu-id="9dcdb-339">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-340">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9dcdb-341">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-342">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-343">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9dcdb-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9dcdb-345">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-345">Empty</span></span> | <span data-ttu-id="9dcdb-346">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9dcdb-347">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9dcdb-348">JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9dcdb-349">Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9dcdb-350">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9dcdb-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9dcdb-351">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-352">ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9dcdb-353">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9dcdb-354">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9dcdb-355">`AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9dcdb-356">Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9dcdb-357">Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9dcdb-358">Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9dcdb-359">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9dcdb-360">.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9dcdb-361">Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9dcdb-362">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9dcdb-363">Newtonsoft. JSON öğesine geç</span><span class="sxs-lookup"><span data-stu-id="9dcdb-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9dcdb-364">' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9dcdb-365">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-365">MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-366">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9dcdb-367">Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9dcdb-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-368">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9dcdb-369">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-369">Configure server options</span></span>

<span data-ttu-id="9dcdb-370">Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9dcdb-371">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-371">Option</span></span> | <span data-ttu-id="9dcdb-372">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-372">Default Value</span></span> | <span data-ttu-id="9dcdb-373">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9dcdb-374">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-374">30 seconds</span></span> | <span data-ttu-id="9dcdb-375">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9dcdb-376">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9dcdb-377">Önerilen değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-378">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-378">15 seconds</span></span> | <span data-ttu-id="9dcdb-379">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9dcdb-380">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-381">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-382">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-382">15 seconds</span></span> | <span data-ttu-id="9dcdb-383">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9dcdb-384">Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9dcdb-385">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9dcdb-386">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9dcdb-386">All installed protocols</span></span> | <span data-ttu-id="9dcdb-387">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-387">Protocols supported by this hub.</span></span> <span data-ttu-id="9dcdb-388">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9dcdb-389">İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9dcdb-390">Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9dcdb-391">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9dcdb-392">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9dcdb-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-393">32 KB</span></span> | <span data-ttu-id="9dcdb-394">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9dcdb-395">Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9dcdb-396">Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9dcdb-397">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9dcdb-398">Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9dcdb-399">Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9dcdb-400">Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9dcdb-401">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-401">Option</span></span> | <span data-ttu-id="9dcdb-402">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-402">Default Value</span></span> | <span data-ttu-id="9dcdb-403">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9dcdb-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-404">32 KB</span></span> | <span data-ttu-id="9dcdb-405">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9dcdb-406">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9dcdb-407">Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9dcdb-408">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9dcdb-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-409">32 KB</span></span> | <span data-ttu-id="9dcdb-410">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9dcdb-411">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9dcdb-412">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-412">All Transports are enabled.</span></span> | <span data-ttu-id="9dcdb-413">Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9dcdb-414">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-414">See below.</span></span> | <span data-ttu-id="9dcdb-415">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9dcdb-416">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-416">See below.</span></span> | <span data-ttu-id="9dcdb-417">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9dcdb-418">0</span><span class="sxs-lookup"><span data-stu-id="9dcdb-418">0</span></span> | <span data-ttu-id="9dcdb-419">Negotiate protokolünün en düşük sürümünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9dcdb-420">Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9dcdb-421">Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9dcdb-422">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-422">Option</span></span> | <span data-ttu-id="9dcdb-423">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-423">Default Value</span></span> | <span data-ttu-id="9dcdb-424">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9dcdb-425">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-425">90 seconds</span></span> | <span data-ttu-id="9dcdb-426">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9dcdb-427">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9dcdb-428">WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9dcdb-429">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-429">Option</span></span> | <span data-ttu-id="9dcdb-430">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-430">Default Value</span></span> | <span data-ttu-id="9dcdb-431">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9dcdb-432">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-432">5 seconds</span></span> | <span data-ttu-id="9dcdb-433">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9dcdb-434">`Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9dcdb-435">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9dcdb-436">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-436">Configure client options</span></span>

<span data-ttu-id="9dcdb-437">İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9dcdb-438">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9dcdb-439">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-439">Configure logging</span></span>

<span data-ttu-id="9dcdb-440">Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9dcdb-441">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9dcdb-442">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-443">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9dcdb-444">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9dcdb-445">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9dcdb-446">`AddConsole` Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9dcdb-447">JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9dcdb-448">Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9dcdb-449">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9dcdb-450">Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9dcdb-451">Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9dcdb-452">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-452">The following table lists the available log levels.</span></span> <span data-ttu-id="9dcdb-453">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9dcdb-454">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9dcdb-455">Dize</span><span class="sxs-lookup"><span data-stu-id="9dcdb-455">String</span></span>                      | <span data-ttu-id="9dcdb-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9dcdb-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9dcdb-457">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9dcdb-458">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9dcdb-459">Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9dcdb-460">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9dcdb-461">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9dcdb-462">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9dcdb-463">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9dcdb-464">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9dcdb-465">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9dcdb-466">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-466">Configure allowed transports</span></span>

<span data-ttu-id="9dcdb-467">SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9dcdb-468">Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9dcdb-469">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-469">All transports are enabled by default.</span></span>

<span data-ttu-id="9dcdb-470">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9dcdb-471">JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9dcdb-472">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9dcdb-473">Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9dcdb-474">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9dcdb-475">SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9dcdb-476">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-476">Configure bearer authentication</span></span>

<span data-ttu-id="9dcdb-477">Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9dcdb-478">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9dcdb-479">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9dcdb-480">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9dcdb-481">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9dcdb-482">JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9dcdb-483">SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9dcdb-484">[Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9dcdb-485">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9dcdb-486">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9dcdb-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9dcdb-487">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-488">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-489">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-489">Option</span></span> | <span data-ttu-id="9dcdb-490">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-490">Default value</span></span> | <span data-ttu-id="9dcdb-491">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9dcdb-492">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-493">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-493">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-494">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-495">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-496">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-497">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-497">15 seconds</span></span> | <span data-ttu-id="9dcdb-498">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-499">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-500">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-501">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-502">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-502">15 seconds</span></span> | <span data-ttu-id="9dcdb-503">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-504">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-505">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9dcdb-506">.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-508">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-508">Option</span></span> | <span data-ttu-id="9dcdb-509">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-509">Default value</span></span> | <span data-ttu-id="9dcdb-510">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9dcdb-511">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-512">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-512">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-513">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9dcdb-514">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-515">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9dcdb-516">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-517">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-518">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-519">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-520">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-520">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-521">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-521">Option</span></span> | <span data-ttu-id="9dcdb-522">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-522">Default value</span></span> | <span data-ttu-id="9dcdb-523">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9dcdb-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9dcdb-525">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-526">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-526">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-527">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-528">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-529">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9dcdb-530">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-530">15 seconds</span></span> | <span data-ttu-id="9dcdb-531">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-532">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-533">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-534">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9dcdb-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9dcdb-536">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-537">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-538">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-539">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9dcdb-540">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-540">Configure additional options</span></span>

<span data-ttu-id="9dcdb-541">Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-542">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-543">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-543">.NET Option</span></span> |  <span data-ttu-id="9dcdb-544">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-544">Default value</span></span> | <span data-ttu-id="9dcdb-545">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-546">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9dcdb-547">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-548">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-549">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9dcdb-550">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-550">Empty</span></span> | <span data-ttu-id="9dcdb-551">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9dcdb-552">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-552">Empty</span></span> | <span data-ttu-id="9dcdb-553">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9dcdb-554">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-554">Empty</span></span> | <span data-ttu-id="9dcdb-555">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9dcdb-556">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-556">5 seconds</span></span> | <span data-ttu-id="9dcdb-557">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-557">WebSockets only.</span></span> <span data-ttu-id="9dcdb-558">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9dcdb-559">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9dcdb-560">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-560">Empty</span></span> | <span data-ttu-id="9dcdb-561">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9dcdb-562">HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9dcdb-563">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="9dcdb-564">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9dcdb-565">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9dcdb-566">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9dcdb-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9dcdb-567">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9dcdb-568">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9dcdb-569">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9dcdb-570">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9dcdb-571">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-573">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-573">JavaScript Option</span></span> | <span data-ttu-id="9dcdb-574">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-574">Default Value</span></span> | <span data-ttu-id="9dcdb-575">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9dcdb-576">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9dcdb-577">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-578">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-579">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-580">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-580">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-581">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-581">Java Option</span></span> | <span data-ttu-id="9dcdb-582">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-582">Default Value</span></span> | <span data-ttu-id="9dcdb-583">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-584">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9dcdb-585">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-586">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-587">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9dcdb-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9dcdb-589">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-589">Empty</span></span> | <span data-ttu-id="9dcdb-590">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9dcdb-591">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9dcdb-592">JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9dcdb-593">Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9dcdb-594">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9dcdb-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9dcdb-595">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-596">ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9dcdb-597">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9dcdb-598">JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9dcdb-599">`AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9dcdb-600">Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9dcdb-601">Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9dcdb-602">Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9dcdb-603">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9dcdb-604">.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9dcdb-605">Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9dcdb-606">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9dcdb-607">Newtonsoft. JSON öğesine geç</span><span class="sxs-lookup"><span data-stu-id="9dcdb-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9dcdb-608">' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9dcdb-609">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-609">MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-610">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9dcdb-611">Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9dcdb-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-612">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9dcdb-613">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-613">Configure server options</span></span>

<span data-ttu-id="9dcdb-614">Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9dcdb-615">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-615">Option</span></span> | <span data-ttu-id="9dcdb-616">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-616">Default Value</span></span> | <span data-ttu-id="9dcdb-617">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9dcdb-618">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-618">30 seconds</span></span> | <span data-ttu-id="9dcdb-619">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9dcdb-620">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9dcdb-621">Önerilen değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-622">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-622">15 seconds</span></span> | <span data-ttu-id="9dcdb-623">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9dcdb-624">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-625">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-626">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-626">15 seconds</span></span> | <span data-ttu-id="9dcdb-627">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9dcdb-628">Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9dcdb-629">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9dcdb-630">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9dcdb-630">All installed protocols</span></span> | <span data-ttu-id="9dcdb-631">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-631">Protocols supported by this hub.</span></span> <span data-ttu-id="9dcdb-632">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9dcdb-633">İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9dcdb-634">Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9dcdb-635">İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9dcdb-636">Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9dcdb-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-637">32 KB</span></span> | <span data-ttu-id="9dcdb-638">Tek bir gelen hub iletisinin en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9dcdb-639">Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9dcdb-640">Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9dcdb-641">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9dcdb-642">Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9dcdb-643">Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9dcdb-644">Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9dcdb-645">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-645">Option</span></span> | <span data-ttu-id="9dcdb-646">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-646">Default Value</span></span> | <span data-ttu-id="9dcdb-647">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9dcdb-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-648">32 KB</span></span> | <span data-ttu-id="9dcdb-649">İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9dcdb-650">Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9dcdb-651">Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9dcdb-652">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9dcdb-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-653">32 KB</span></span> | <span data-ttu-id="9dcdb-654">Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9dcdb-655">Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9dcdb-656">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-656">All Transports are enabled.</span></span> | <span data-ttu-id="9dcdb-657">Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9dcdb-658">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-658">See below.</span></span> | <span data-ttu-id="9dcdb-659">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9dcdb-660">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-660">See below.</span></span> | <span data-ttu-id="9dcdb-661">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9dcdb-662">Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9dcdb-663">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-663">Option</span></span> | <span data-ttu-id="9dcdb-664">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-664">Default Value</span></span> | <span data-ttu-id="9dcdb-665">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9dcdb-666">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-666">90 seconds</span></span> | <span data-ttu-id="9dcdb-667">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9dcdb-668">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9dcdb-669">WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9dcdb-670">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-670">Option</span></span> | <span data-ttu-id="9dcdb-671">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-671">Default Value</span></span> | <span data-ttu-id="9dcdb-672">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9dcdb-673">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-673">5 seconds</span></span> | <span data-ttu-id="9dcdb-674">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9dcdb-675">`Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9dcdb-676">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9dcdb-677">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-677">Configure client options</span></span>

<span data-ttu-id="9dcdb-678">İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9dcdb-679">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9dcdb-680">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-680">Configure logging</span></span>

<span data-ttu-id="9dcdb-681">Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9dcdb-682">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9dcdb-683">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-684">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9dcdb-685">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9dcdb-686">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9dcdb-687">`AddConsole` Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9dcdb-688">JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9dcdb-689">Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9dcdb-690">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9dcdb-691">Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9dcdb-692">Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9dcdb-693">Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-693">The following table lists the available log levels.</span></span> <span data-ttu-id="9dcdb-694">Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9dcdb-695">Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9dcdb-696">Dize</span><span class="sxs-lookup"><span data-stu-id="9dcdb-696">String</span></span>                      | <span data-ttu-id="9dcdb-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9dcdb-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9dcdb-698">`info`**ya da**`information`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9dcdb-699">`warn`**ya da**`warning`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9dcdb-700">Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9dcdb-701">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9dcdb-702">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9dcdb-703">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9dcdb-704">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9dcdb-705">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9dcdb-706">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9dcdb-707">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-707">Configure allowed transports</span></span>

<span data-ttu-id="9dcdb-708">SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9dcdb-709">Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9dcdb-710">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-710">All transports are enabled by default.</span></span>

<span data-ttu-id="9dcdb-711">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9dcdb-712">JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9dcdb-713">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9dcdb-714">Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9dcdb-715">Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9dcdb-716">SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9dcdb-717">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-717">Configure bearer authentication</span></span>

<span data-ttu-id="9dcdb-718">Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9dcdb-719">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9dcdb-720">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9dcdb-721">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9dcdb-722">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9dcdb-723">JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9dcdb-724">SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9dcdb-725">[Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9dcdb-726">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9dcdb-727">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9dcdb-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9dcdb-728">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-729">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-730">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-730">Option</span></span> | <span data-ttu-id="9dcdb-731">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-731">Default value</span></span> | <span data-ttu-id="9dcdb-732">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9dcdb-733">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-734">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-734">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-735">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-736">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-737">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-738">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-738">15 seconds</span></span> | <span data-ttu-id="9dcdb-739">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-740">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-741">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-742">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-743">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-743">15 seconds</span></span> | <span data-ttu-id="9dcdb-744">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-745">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-746">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9dcdb-747">.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-749">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-749">Option</span></span> | <span data-ttu-id="9dcdb-750">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-750">Default value</span></span> | <span data-ttu-id="9dcdb-751">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9dcdb-752">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-753">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-753">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-754">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9dcdb-755">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-756">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9dcdb-757">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-758">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-759">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-760">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-761">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-761">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-762">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-762">Option</span></span> | <span data-ttu-id="9dcdb-763">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-763">Default value</span></span> | <span data-ttu-id="9dcdb-764">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9dcdb-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9dcdb-766">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-767">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-767">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-768">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-769">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-770">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9dcdb-771">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-771">15 seconds</span></span> | <span data-ttu-id="9dcdb-772">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-773">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-774">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-775">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9dcdb-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9dcdb-777">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-778">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-779">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-780">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9dcdb-781">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-781">Configure additional options</span></span>

<span data-ttu-id="9dcdb-782">Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-783">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-784">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-784">.NET Option</span></span> |  <span data-ttu-id="9dcdb-785">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-785">Default value</span></span> | <span data-ttu-id="9dcdb-786">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-787">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9dcdb-788">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-789">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-790">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9dcdb-791">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-791">Empty</span></span> | <span data-ttu-id="9dcdb-792">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9dcdb-793">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-793">Empty</span></span> | <span data-ttu-id="9dcdb-794">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9dcdb-795">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-795">Empty</span></span> | <span data-ttu-id="9dcdb-796">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9dcdb-797">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-797">5 seconds</span></span> | <span data-ttu-id="9dcdb-798">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-798">WebSockets only.</span></span> <span data-ttu-id="9dcdb-799">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9dcdb-800">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9dcdb-801">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-801">Empty</span></span> | <span data-ttu-id="9dcdb-802">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9dcdb-803">HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9dcdb-804">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="9dcdb-805">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9dcdb-806">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9dcdb-807">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9dcdb-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9dcdb-808">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9dcdb-809">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9dcdb-810">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9dcdb-811">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9dcdb-812">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-814">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-814">JavaScript Option</span></span> | <span data-ttu-id="9dcdb-815">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-815">Default Value</span></span> | <span data-ttu-id="9dcdb-816">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9dcdb-817">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9dcdb-818">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-819">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-820">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-821">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-821">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-822">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-822">Java Option</span></span> | <span data-ttu-id="9dcdb-823">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-823">Default Value</span></span> | <span data-ttu-id="9dcdb-824">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-825">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9dcdb-826">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-827">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-828">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9dcdb-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9dcdb-830">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-830">Empty</span></span> | <span data-ttu-id="9dcdb-831">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9dcdb-832">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9dcdb-833">JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9dcdb-834">Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9dcdb-835">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9dcdb-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9dcdb-836">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-837">ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9dcdb-838">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9dcdb-839">JSON serileştirme, yöntekinizdeki `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9dcdb-840">Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9dcdb-841">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, bağımsız değişkenlerin ve dönüş `JsonSerializerSettings` değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9dcdb-842">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9dcdb-843">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9dcdb-844">.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9dcdb-845">Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9dcdb-846">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9dcdb-847">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-847">MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-848">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9dcdb-849">Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9dcdb-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-850">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9dcdb-851">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-851">Configure server options</span></span>

<span data-ttu-id="9dcdb-852">Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9dcdb-853">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-853">Option</span></span> | <span data-ttu-id="9dcdb-854">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-854">Default Value</span></span> | <span data-ttu-id="9dcdb-855">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9dcdb-856">30 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-856">30 seconds</span></span> | <span data-ttu-id="9dcdb-857">Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9dcdb-858">Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9dcdb-859">Önerilen değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-860">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-860">15 seconds</span></span> | <span data-ttu-id="9dcdb-861">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9dcdb-862">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-863">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-864">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-864">15 seconds</span></span> | <span data-ttu-id="9dcdb-865">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9dcdb-866">Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9dcdb-867">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9dcdb-868">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9dcdb-868">All installed protocols</span></span> | <span data-ttu-id="9dcdb-869">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-869">Protocols supported by this hub.</span></span> <span data-ttu-id="9dcdb-870">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9dcdb-871">İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9dcdb-872">Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9dcdb-873">Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9dcdb-874">Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9dcdb-875">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9dcdb-876">Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9dcdb-877">Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9dcdb-878">Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9dcdb-879">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-879">Option</span></span> | <span data-ttu-id="9dcdb-880">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-880">Default Value</span></span> | <span data-ttu-id="9dcdb-881">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9dcdb-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-882">32 KB</span></span> | <span data-ttu-id="9dcdb-883">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9dcdb-884">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9dcdb-885">Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9dcdb-886">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9dcdb-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-887">32 KB</span></span> | <span data-ttu-id="9dcdb-888">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9dcdb-889">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9dcdb-890">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-890">All Transports are enabled.</span></span> | <span data-ttu-id="9dcdb-891">Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9dcdb-892">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-892">See below.</span></span> | <span data-ttu-id="9dcdb-893">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9dcdb-894">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-894">See below.</span></span> | <span data-ttu-id="9dcdb-895">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9dcdb-896">Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9dcdb-897">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-897">Option</span></span> | <span data-ttu-id="9dcdb-898">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-898">Default Value</span></span> | <span data-ttu-id="9dcdb-899">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9dcdb-900">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-900">90 seconds</span></span> | <span data-ttu-id="9dcdb-901">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9dcdb-902">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9dcdb-903">WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9dcdb-904">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-904">Option</span></span> | <span data-ttu-id="9dcdb-905">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-905">Default Value</span></span> | <span data-ttu-id="9dcdb-906">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9dcdb-907">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-907">5 seconds</span></span> | <span data-ttu-id="9dcdb-908">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9dcdb-909">`Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9dcdb-910">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9dcdb-911">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-911">Configure client options</span></span>

<span data-ttu-id="9dcdb-912">İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9dcdb-913">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9dcdb-914">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-914">Configure logging</span></span>

<span data-ttu-id="9dcdb-915">Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9dcdb-916">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9dcdb-917">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-918">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9dcdb-919">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9dcdb-920">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9dcdb-921">`AddConsole` Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9dcdb-922">JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9dcdb-923">Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9dcdb-924">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9dcdb-925">Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9dcdb-926">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9dcdb-927">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9dcdb-928">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9dcdb-929">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9dcdb-930">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9dcdb-931">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9dcdb-932">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-932">Configure allowed transports</span></span>

<span data-ttu-id="9dcdb-933">SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9dcdb-934">Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9dcdb-935">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-935">All transports are enabled by default.</span></span>

<span data-ttu-id="9dcdb-936">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9dcdb-937">JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9dcdb-938">Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9dcdb-939">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-939">Configure bearer authentication</span></span>

<span data-ttu-id="9dcdb-940">Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9dcdb-941">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9dcdb-942">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9dcdb-943">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9dcdb-944">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9dcdb-945">JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9dcdb-946">SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9dcdb-947">[Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9dcdb-948">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9dcdb-949">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9dcdb-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9dcdb-950">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-951">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-952">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-952">Option</span></span> | <span data-ttu-id="9dcdb-953">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-953">Default value</span></span> | <span data-ttu-id="9dcdb-954">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9dcdb-955">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-956">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-956">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-957">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-958">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-959">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-960">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-960">15 seconds</span></span> | <span data-ttu-id="9dcdb-961">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-962">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-963">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-964">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-965">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-965">15 seconds</span></span> | <span data-ttu-id="9dcdb-966">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-967">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-968">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9dcdb-969">.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-971">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-971">Option</span></span> | <span data-ttu-id="9dcdb-972">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-972">Default value</span></span> | <span data-ttu-id="9dcdb-973">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9dcdb-974">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-975">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-975">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-976">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9dcdb-977">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-978">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9dcdb-979">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-980">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-981">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-982">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-983">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-983">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-984">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-984">Option</span></span> | <span data-ttu-id="9dcdb-985">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-985">Default value</span></span> | <span data-ttu-id="9dcdb-986">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9dcdb-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9dcdb-988">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-989">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-989">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-990">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-991">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-992">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9dcdb-993">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-993">15 seconds</span></span> | <span data-ttu-id="9dcdb-994">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-995">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-996">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-997">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9dcdb-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9dcdb-999">15 saniye (15.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-1000">İstemcinin ping iletileri gönderdiği aralığı belirler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9dcdb-1001">İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9dcdb-1002">İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9dcdb-1003">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1003">Configure additional options</span></span>

<span data-ttu-id="9dcdb-1004">Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-1006">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1006">.NET Option</span></span> |  <span data-ttu-id="9dcdb-1007">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1007">Default value</span></span> | <span data-ttu-id="9dcdb-1008">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-1009">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9dcdb-1010">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1011">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1012">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9dcdb-1013">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1013">Empty</span></span> | <span data-ttu-id="9dcdb-1014">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9dcdb-1015">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1015">Empty</span></span> | <span data-ttu-id="9dcdb-1016">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9dcdb-1017">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1017">Empty</span></span> | <span data-ttu-id="9dcdb-1018">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9dcdb-1019">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1019">5 seconds</span></span> | <span data-ttu-id="9dcdb-1020">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1020">WebSockets only.</span></span> <span data-ttu-id="9dcdb-1021">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9dcdb-1022">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9dcdb-1023">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1023">Empty</span></span> | <span data-ttu-id="9dcdb-1024">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9dcdb-1025">HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9dcdb-1026">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="9dcdb-1027">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9dcdb-1028">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9dcdb-1029">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9dcdb-1030">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9dcdb-1031">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9dcdb-1032">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9dcdb-1033">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9dcdb-1034">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-1036">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1036">JavaScript Option</span></span> | <span data-ttu-id="9dcdb-1037">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1037">Default Value</span></span> | <span data-ttu-id="9dcdb-1038">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9dcdb-1039">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9dcdb-1040">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1041">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1042">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-1043">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-1044">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1044">Java Option</span></span> | <span data-ttu-id="9dcdb-1045">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1045">Default Value</span></span> | <span data-ttu-id="9dcdb-1046">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-1047">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9dcdb-1048">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1049">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1050">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9dcdb-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9dcdb-1052">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1052">Empty</span></span> | <span data-ttu-id="9dcdb-1053">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9dcdb-1054">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9dcdb-1055">JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9dcdb-1056">Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9dcdb-1057">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9dcdb-1058">JSON/MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-1059">ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9dcdb-1060">Her protokol serileştirme yapılandırma seçenekleri vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9dcdb-1061">JSON serileştirme, yöntekinizdeki `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9dcdb-1062">Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9dcdb-1063">Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, bağımsız değişkenlerin ve dönüş `JsonSerializerSettings` değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9dcdb-1064">Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9dcdb-1065">Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9dcdb-1066">.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9dcdb-1067">Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9dcdb-1068">JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9dcdb-1069">MessagePack serileştirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1069">MessagePack serialization options</span></span>

<span data-ttu-id="9dcdb-1070">MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9dcdb-1071">Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-1072">Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9dcdb-1073">Sunucu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1073">Configure server options</span></span>

<span data-ttu-id="9dcdb-1074">Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9dcdb-1075">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1075">Option</span></span> | <span data-ttu-id="9dcdb-1076">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1076">Default Value</span></span> | <span data-ttu-id="9dcdb-1077">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-1078">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1078">15 seconds</span></span> | <span data-ttu-id="9dcdb-1079">İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9dcdb-1080">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-1081">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9dcdb-1082">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1082">15 seconds</span></span> | <span data-ttu-id="9dcdb-1083">Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9dcdb-1084">Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9dcdb-1085">`ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9dcdb-1086">Tüm yüklü protokoller</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1086">All installed protocols</span></span> | <span data-ttu-id="9dcdb-1087">Bu hub tarafından desteklenen protokoller.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="9dcdb-1088">Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9dcdb-1089">İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9dcdb-1090">Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9dcdb-1091">Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9dcdb-1092">Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9dcdb-1093">Gelişmiş HTTP yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9dcdb-1094">Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9dcdb-1095">Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9dcdb-1096">Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9dcdb-1097">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1097">Option</span></span> | <span data-ttu-id="9dcdb-1098">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1098">Default Value</span></span> | <span data-ttu-id="9dcdb-1099">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9dcdb-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1100">32 KB</span></span> | <span data-ttu-id="9dcdb-1101">İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9dcdb-1102">Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9dcdb-1103">Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9dcdb-1104">Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9dcdb-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1105">32 KB</span></span> | <span data-ttu-id="9dcdb-1106">Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9dcdb-1107">Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9dcdb-1108">Tüm aktarımlar etkin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1108">All Transports are enabled.</span></span> | <span data-ttu-id="9dcdb-1109">Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9dcdb-1110">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1110">See below.</span></span> | <span data-ttu-id="9dcdb-1111">Uzun yoklama taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9dcdb-1112">Aşağıya bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1112">See below.</span></span> | <span data-ttu-id="9dcdb-1113">WebSockets taşımasına özgü ek seçenekler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9dcdb-1114">Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9dcdb-1115">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1115">Option</span></span> | <span data-ttu-id="9dcdb-1116">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1116">Default Value</span></span> | <span data-ttu-id="9dcdb-1117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9dcdb-1118">90 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1118">90 seconds</span></span> | <span data-ttu-id="9dcdb-1119">Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9dcdb-1120">Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9dcdb-1121">WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9dcdb-1122">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1122">Option</span></span> | <span data-ttu-id="9dcdb-1123">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1123">Default Value</span></span> | <span data-ttu-id="9dcdb-1124">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9dcdb-1125">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1125">5 seconds</span></span> | <span data-ttu-id="9dcdb-1126">Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9dcdb-1127">`Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9dcdb-1128">Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9dcdb-1129">İstemci seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1129">Configure client options</span></span>

<span data-ttu-id="9dcdb-1130">İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9dcdb-1131">Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9dcdb-1132">Günlüğe kaydetmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1132">Configure logging</span></span>

<span data-ttu-id="9dcdb-1133">Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9dcdb-1134">Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9dcdb-1135">Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9dcdb-1136">Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9dcdb-1137">Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9dcdb-1138">Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9dcdb-1139">`AddConsole` Uzantı yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9dcdb-1140">JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9dcdb-1141">Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9dcdb-1142">Günlükler tarayıcı konsolu penceresine yazılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9dcdb-1143">Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9dcdb-1144">Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9dcdb-1145">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9dcdb-1146">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9dcdb-1147">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9dcdb-1148">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9dcdb-1149">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9dcdb-1150">İzin verilen taşımaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1150">Configure allowed transports</span></span>

<span data-ttu-id="9dcdb-1151">SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9dcdb-1152">Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9dcdb-1153">Tüm aktarımlar varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="9dcdb-1154">Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9dcdb-1155">JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9dcdb-1156">Taşıyıcı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1156">Configure bearer authentication</span></span>

<span data-ttu-id="9dcdb-1157">Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9dcdb-1158">.NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9dcdb-1159">JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9dcdb-1160">Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9dcdb-1161">.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9dcdb-1162">JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9dcdb-1163">SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9dcdb-1164">[Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9dcdb-1165">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9dcdb-1166">Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9dcdb-1167">Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-1169">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1169">Option</span></span> | <span data-ttu-id="9dcdb-1170">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1170">Default value</span></span> | <span data-ttu-id="9dcdb-1171">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9dcdb-1172">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-1173">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1173">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-1174">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-1175">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-1176">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9dcdb-1177">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1177">15 seconds</span></span> | <span data-ttu-id="9dcdb-1178">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-1179">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9dcdb-1180">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-1181">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9dcdb-1182">.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-1184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1184">Option</span></span> | <span data-ttu-id="9dcdb-1185">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1185">Default value</span></span> | <span data-ttu-id="9dcdb-1186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9dcdb-1187">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-1188">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1188">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-1189">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9dcdb-1190">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-1191">Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-1192">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-1193">Seçenek</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1193">Option</span></span> | <span data-ttu-id="9dcdb-1194">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1194">Default value</span></span> | <span data-ttu-id="9dcdb-1195">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9dcdb-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9dcdb-1197">30 saniye (30.000 milisaniye)</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9dcdb-1198">Sunucu etkinliği için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1198">Timeout for server activity.</span></span> <span data-ttu-id="9dcdb-1199">Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-1200">Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9dcdb-1201">Önerilen değer, ping bir sürenin gelmesi için en az iki `KeepAliveInterval` değerin değeri olan bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9dcdb-1202">15 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1202">15 seconds</span></span> | <span data-ttu-id="9dcdb-1203">İlk sunucu el sıkışması için zaman aşımı.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="9dcdb-1204">Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9dcdb-1205">Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9dcdb-1206">El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9dcdb-1207">Ek seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1207">Configure additional options</span></span>

<span data-ttu-id="9dcdb-1208">Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9dcdb-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9dcdb-1210">.NET seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1210">.NET Option</span></span> |  <span data-ttu-id="9dcdb-1211">Varsayılan değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1211">Default value</span></span> | <span data-ttu-id="9dcdb-1212">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-1213">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9dcdb-1214">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1215">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1216">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9dcdb-1217">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1217">Empty</span></span> | <span data-ttu-id="9dcdb-1218">Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9dcdb-1219">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1219">Empty</span></span> | <span data-ttu-id="9dcdb-1220">Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9dcdb-1221">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1221">Empty</span></span> | <span data-ttu-id="9dcdb-1222">Her HTTP isteğiyle gönderilen kimlik bilgileri.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9dcdb-1223">5 saniye</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1223">5 seconds</span></span> | <span data-ttu-id="9dcdb-1224">Yalnızca WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1224">WebSockets only.</span></span> <span data-ttu-id="9dcdb-1225">Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9dcdb-1226">Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9dcdb-1227">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1227">Empty</span></span> | <span data-ttu-id="9dcdb-1228">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9dcdb-1229">HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9dcdb-1230">WebSocket bağlantıları için kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="9dcdb-1231">Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9dcdb-1232">Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9dcdb-1233">**İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9dcdb-1234">HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9dcdb-1235">Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9dcdb-1236">Bu, Windows kimlik doğrulamasının kullanılmasını mümkün.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9dcdb-1237">Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9dcdb-1238">Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9dcdb-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9dcdb-1240">JavaScript seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1240">JavaScript Option</span></span> | <span data-ttu-id="9dcdb-1241">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1241">Default Value</span></span> | <span data-ttu-id="9dcdb-1242">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9dcdb-1243">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9dcdb-1244">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1245">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1246">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9dcdb-1247">Java</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="9dcdb-1248">Java seçeneği</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1248">Java Option</span></span> | <span data-ttu-id="9dcdb-1249">Varsayılan Değer</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1249">Default Value</span></span> | <span data-ttu-id="9dcdb-1250">Açıklama</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9dcdb-1251">HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9dcdb-1252">Anlaşma adımını atlamak `true` için bunu olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9dcdb-1253">**Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9dcdb-1254">Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9dcdb-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9dcdb-1256">Olmamalıdır</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1256">Empty</span></span> | <span data-ttu-id="9dcdb-1257">Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası.</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9dcdb-1258">.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9dcdb-1259">JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9dcdb-1260">Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9dcdb-1261">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9dcdb-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
