---
title: SignalRVe ASP.NET Core arasındaki farklarSignalR
author: bradygaster
description: SignalRVe ASP.NET Core arasındaki farklarSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: d63e9077ba23c3783d7ce1ceb9c9683bcb0d3a29
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408896"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="b5e0c-103">ASP.NET SignalR ve ASP.NET Core arasındaki farklarSignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="b5e0c-104">ASP.NET Core SignalR , ASP.NET için istemcilerle veya sunucularla uyumlu değildir SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="b5e0c-105">Bu makalede, ASP.NET Core kaldırılan veya değiştirilen özellikler ayrıntılı olarak anlatılmaktadır SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="b5e0c-106">SignalRSürümü belirleme</span><span class="sxs-lookup"><span data-stu-id="b5e0c-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="b5e0c-107">ASP.NETSignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-107">ASP.NET SignalR</span></span> | <span data-ttu-id="b5e0c-108">ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="b5e0c-109">Sunucu NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-109">Server NuGet package</span></span> | <span data-ttu-id="b5e0c-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="b5e0c-111">Yok.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-111">None.</span></span> <span data-ttu-id="b5e0c-112">[Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) Shared Framework 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="b5e0c-113">İstemci NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-113">Client NuGet packages</span></span> | <span data-ttu-id="b5e0c-114">[Microsoft. AspNet. SignalR . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="b5e0c-115">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="b5e0c-116">[Microsoft. AspNetCore. SignalR . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="b5e0c-117">JavaScript istemcisi NPM paketi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-117">JavaScript client npm package</span></span> | [<span data-ttu-id="b5e0c-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="b5e0c-119">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-119">Java client</span></span> | <span data-ttu-id="b5e0c-120">[GitHub deposu](https://github.com/SignalR/java-client) (kullanım dışı)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="b5e0c-121">Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="b5e0c-122">Sunucu uygulaması türü</span><span class="sxs-lookup"><span data-stu-id="b5e0c-122">Server app type</span></span> | <span data-ttu-id="b5e0c-123">ASP.NET (System. Web) veya OWıN Self-Host</span><span class="sxs-lookup"><span data-stu-id="b5e0c-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="b5e0c-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5e0c-124">ASP.NET Core</span></span> |
| <span data-ttu-id="b5e0c-125">Desteklenen sunucu platformları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-125">Supported server platforms</span></span> | <span data-ttu-id="b5e0c-126">.NET Framework 4,5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="b5e0c-127">.NET Core 3,0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="b5e0c-128">ASP.NETSignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-128">ASP.NET SignalR</span></span> | <span data-ttu-id="b5e0c-129">ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="b5e0c-130">Sunucu NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-130">Server NuGet package</span></span> | <span data-ttu-id="b5e0c-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="b5e0c-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="b5e0c-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="b5e0c-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-134">(.NET Framework)</span></span> |
| <span data-ttu-id="b5e0c-135">İstemci NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-135">Client NuGet packages</span></span> | <span data-ttu-id="b5e0c-136">[Microsoft. AspNet. SignalR . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="b5e0c-137">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="b5e0c-138">[Microsoft. AspNetCore. SignalR . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="b5e0c-139">JavaScript istemcisi NPM paketi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-139">JavaScript client npm package</span></span> | [<span data-ttu-id="b5e0c-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="b5e0c-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="b5e0c-141">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-141">Java client</span></span> | <span data-ttu-id="b5e0c-142">[GitHub deposu](https://github.com/SignalR/java-client) (kullanım dışı)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="b5e0c-143">Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="b5e0c-144">Sunucu uygulaması türü</span><span class="sxs-lookup"><span data-stu-id="b5e0c-144">Server app type</span></span> | <span data-ttu-id="b5e0c-145">ASP.NET (System. Web) veya OWıN Self-Host</span><span class="sxs-lookup"><span data-stu-id="b5e0c-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="b5e0c-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5e0c-146">ASP.NET Core</span></span> |
| <span data-ttu-id="b5e0c-147">Desteklenen sunucu platformları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-147">Supported server platforms</span></span> | <span data-ttu-id="b5e0c-148">.NET Framework 4,5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="b5e0c-149">.NET Framework 4.6.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="b5e0c-150">.NET Core 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="b5e0c-151">Özellik farklılıkları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="b5e0c-152">Otomatik yeniden bağlantılar</span><span class="sxs-lookup"><span data-stu-id="b5e0c-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5e0c-153">ASP.NET içinde SignalR :</span><span class="sxs-lookup"><span data-stu-id="b5e0c-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="b5e0c-154">Varsayılan olarak, SignalR bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="b5e0c-155">ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="b5e0c-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="b5e0c-156">Otomatik yeniden bağlantılar hem [.net istemcisiyle](xref:signalr/dotnet-client#automatically-reconnect) hem de [JavaScript istemcisiyle](xref:signalr/javascript-client#automatically-reconnect)birlikte kabul edilir:</span><span class="sxs-lookup"><span data-stu-id="b5e0c-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5e0c-157">ASP.NET Core 3,0 ' dan önce SignalR Otomatik yeniden bağlantılar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="b5e0c-158">İstemcinin bağlantısı kesilirse, kullanıcının yeniden bağlanmak için açık olarak yeni bir bağlantı başlatması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="b5e0c-159">ASP.NET SignalR ' de, SignalR bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="b5e0c-160">Protokol desteği</span><span class="sxs-lookup"><span data-stu-id="b5e0c-160">Protocol support</span></span>

<span data-ttu-id="b5e0c-161">ASP.NET Core SignalR , JSON 'ı ve [MessagePack](xref:signalr/messagepackhubprotocol)tabanlı yeni bir ikili protokolü destekler.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="b5e0c-162">Ek olarak özel protokoller de oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="b5e0c-163">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="b5e0c-163">Transports</span></span>

<span data-ttu-id="b5e0c-164">ASP.NET Core içinde süresiz çerçeve taşıması desteklenmez SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="b5e0c-165">Sunucu farkları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-165">Differences on the server</span></span>

<span data-ttu-id="b5e0c-166">ASP.NET Core SignalR sunucu tarafı kitaplıkları, hem hem de MVC projeleri için **ASP.NET Core Web uygulaması** şablonunda kullanılan [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)'e dahildir Razor .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="b5e0c-167">ASP.NET Core SignalR bir ASP.NET Core ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="b5e0c-168">' De çağırarak yapılandırılması gerekir <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5e0c-169">Yönlendirmeyi yapılandırmak için, yolları yöntemindeki Yöntem çağrısı içindeki hublara eşleyin <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b5e0c-170">Yönlendirmeyi yapılandırmak için, yolları yöntemindeki Yöntem çağrısı içindeki hublara eşleyin <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="b5e0c-171">Yapışkan oturumlar</span><span class="sxs-lookup"><span data-stu-id="b5e0c-171">Sticky sessions</span></span>

<span data-ttu-id="b5e0c-172">ASP.NET için genişleme modeli, SignalR istemcilerin gruptaki herhangi bir sunucuya yeniden bağlanmasına ve ileti gönderemesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="b5e0c-173">ASP.NET Core SignalR , istemcinin bağlantı süresince aynı sunucu ile etkileşimde olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="b5e0c-174">Redsıs kullanarak genişleme için, bu, yapışkan oturumların gerekli olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="b5e0c-175">[Azure SignalR hizmeti](/azure/azure-signalr/)'ni kullanarak genişleme için, hizmet istemcilerle bağlantıları işlediği için yapışkan oturumlar gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="b5e0c-176">Bağlantı başına tek hub</span><span class="sxs-lookup"><span data-stu-id="b5e0c-176">Single hub per connection</span></span>

<span data-ttu-id="b5e0c-177">ASP.NET Core SignalR , bağlantı modeli basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="b5e0c-178">Bağlantılar, birden çok hub 'a erişimi paylaşmak için kullanılan tek bir bağlantı yerine doğrudan tek bir hub 'a yapılır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="b5e0c-179">Akış</span><span class="sxs-lookup"><span data-stu-id="b5e0c-179">Streaming</span></span>

<span data-ttu-id="b5e0c-180">ASP.NET Core SignalR artık hub 'dan istemciye [veri akışını](xref:signalr/streaming) desteklemektedir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="b5e0c-181">Durum</span><span class="sxs-lookup"><span data-stu-id="b5e0c-181">State</span></span>

<span data-ttu-id="b5e0c-182">İstemciler ve merkez arasında rastgele durum geçirme özelliği (genellikle denir `HubState` ) kaldırılmıştır ve ilerleme durumu iletileri için destek de sağlar.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="b5e0c-183">Şu anda hub proxy 'lerinin karşılığı yok.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="b5e0c-184">PersistentConnection kaldırma</span><span class="sxs-lookup"><span data-stu-id="b5e0c-184">PersistentConnection removal</span></span>

<span data-ttu-id="b5e0c-185">ASP.NET Core SignalR , [Persistentconnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) sınıfı kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="b5e0c-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="b5e0c-186">GlobalHost</span></span>

<span data-ttu-id="b5e0c-187">ASP.NET Core çerçeveye yerleşik bağımlılık ekleme (dı) vardır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="b5e0c-188">Hizmetler, [Hubcontext](xref:signalr/hubcontext)'e erışmek için dı kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="b5e0c-189">`GlobalHost`ASP.NET içinde kullanılacak nesne SignalR `HubContext` ASP.NET Core yok SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="b5e0c-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="b5e0c-190">HubPipeline</span></span>

<span data-ttu-id="b5e0c-191">ASP.NET Core SignalR modüller için desteğe sahip değildir `HubPipeline` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="b5e0c-192">İstemci farkları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="b5e0c-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="b5e0c-193">TypeScript</span></span>

<span data-ttu-id="b5e0c-194">ASP.NET Core SignalR Istemcisi [TypeScript](https://www.typescriptlang.org/)dilinde yazılır.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="b5e0c-195">[JavaScript istemcisini](xref:signalr/javascript-client)kullanırken JavaScript veya TypeScript ile yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="b5e0c-196">JavaScript istemcisi NPM 'de barındırılıyor</span><span class="sxs-lookup"><span data-stu-id="b5e0c-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5e0c-197">ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="b5e0c-198">ASP.NET Core sürümlerinde [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM paketi Javascript kitaplıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="b5e0c-199">Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="b5e0c-200">NPM paketini edinmek ve yüklemek için NPM kullanın `@microsoft/signalr` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b5e0c-201">ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="b5e0c-202">ASP.NET Core sürümlerinde [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM paketi Javascript kitaplıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="b5e0c-203">Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="b5e0c-204">NPM paketini edinmek ve yüklemek için NPM kullanın `@aspnet/signalr` .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="b5e0c-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="b5e0c-205">jQuery</span></span>

<span data-ttu-id="b5e0c-206">JQuery üzerindeki bağımlılık kaldırılmıştır, ancak projeler jQuery kullanmaya devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="b5e0c-207">Internet Explorer desteği</span><span class="sxs-lookup"><span data-stu-id="b5e0c-207">Internet Explorer support</span></span>

<span data-ttu-id="b5e0c-208">ASP.NET Core, SignalR Microsoft Internet Explorer 11 veya üstünü gerektirir (ASP.net SignalR desteklenen Microsoft Internet Explorer 8 ve üzeri).</span><span class="sxs-lookup"><span data-stu-id="b5e0c-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="b5e0c-209">JavaScript istemci yöntemi sözdizimi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5e0c-210">JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="b5e0c-211">Nesnesini kullanmak yerine `$connection` , [Hubconnectionbuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="b5e0c-212">Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@microsoft/signalr/HubConnection#on) metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b5e0c-213">JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="b5e0c-214">Nesnesini kullanmak yerine `$connection` , [Hubconnectionbuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="b5e0c-215">Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@aspnet/signalr/HubConnection#on) metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="b5e0c-216">İstemci yöntemini oluşturduktan sonra hub bağlantısını başlatın.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="b5e0c-217">Günlüğe kaydetmek veya hataları işlemek için bir [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodunu zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="b5e0c-218">Hub proxy 'leri</span><span class="sxs-lookup"><span data-stu-id="b5e0c-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5e0c-219">Hub proxy 'leri artık otomatik olarak oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="b5e0c-220">Bunun yerine, yöntem adı [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b5e0c-221">Hub proxy 'leri artık otomatik olarak oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="b5e0c-222">Bunun yerine, yöntem adı [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="b5e0c-223">.NET ve diğer istemciler</span><span class="sxs-lookup"><span data-stu-id="b5e0c-223">.NET and other clients</span></span>

<span data-ttu-id="b5e0c-224">[Microsoft. AspNetCore. SignalR . İstemci](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet paketi, ASP.NET Core için .NET istemci kitaplıklarını içerir SignalR .</span><span class="sxs-lookup"><span data-stu-id="b5e0c-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="b5e0c-225"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>Bir hub bağlantısının örneğini oluşturmak ve derlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="b5e0c-226">Ölçek Genişletme farklılıkları</span><span class="sxs-lookup"><span data-stu-id="b5e0c-226">Scaleout differences</span></span>

<span data-ttu-id="b5e0c-227">ASP.NET SignalR SQL Server ve redsıs destekler.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="b5e0c-228">ASP.NET Core SignalR Azure SignalR hizmeti ve redsıs 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="b5e0c-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="b5e0c-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b5e0c-229">ASP.NET</span></span>

* <span data-ttu-id="b5e0c-230">[SignalRAzure Service Bus ile ölçeği genişletme](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="b5e0c-231">[SignalRRedsıs ile ölçek genişletme](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="b5e0c-232">[SignalRSQL Server ile ölçeği genişletme](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="b5e0c-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5e0c-233">ASP.NET Core</span></span>

* <span data-ttu-id="b5e0c-234">[Azure SignalR hizmeti](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="b5e0c-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="b5e0c-235">Redsıs geri düzlemi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="b5e0c-236">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b5e0c-236">Additional resources</span></span>

* [<span data-ttu-id="b5e0c-237">Merkezler</span><span class="sxs-lookup"><span data-stu-id="b5e0c-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b5e0c-238">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b5e0c-239">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="b5e0c-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b5e0c-240">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="b5e0c-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
