---
title: ':::no-loc(SignalR):::Ve ASP.NET Core arasındaki farklar:::no-loc(SignalR):::'
author: bradygaster
description: ':::no-loc(SignalR):::Ve ASP.NET Core arasındaki farklar:::no-loc(SignalR):::'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061216"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="8d91c-103">ASP.NET :::no-loc(SignalR)::: ve ASP.NET Core arasındaki farklar :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8d91c-103">Differences between ASP.NET :::no-loc(SignalR)::: and ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="8d91c-104">ASP.NET Core :::no-loc(SignalR)::: , ASP.NET için istemcilerle veya sunucularla uyumlu değildir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-104">ASP.NET Core :::no-loc(SignalR)::: isn't compatible with clients or servers for ASP.NET :::no-loc(SignalR):::.</span></span> <span data-ttu-id="8d91c-105">Bu makalede, ASP.NET Core kaldırılan veya değiştirilen özellikler ayrıntılı olarak anlatılmaktadır :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-105">This article details features which have been removed or changed in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="8d91c-106">:::no-loc(SignalR):::Sürümü belirleme</span><span class="sxs-lookup"><span data-stu-id="8d91c-106">How to identify the :::no-loc(SignalR)::: version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="8d91c-107">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8d91c-107">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="8d91c-108">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8d91c-108">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="8d91c-109">**Sunucu NuGet paketi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-109">**Server NuGet package**</span></span> | <span data-ttu-id="8d91c-110">[Microsoft. AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-110">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="8d91c-111">Yok.</span><span class="sxs-lookup"><span data-stu-id="8d91c-111">None.</span></span> <span data-ttu-id="8d91c-112">[Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) Shared Framework 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="8d91c-113">**İstemci NuGet paketleri**</span><span class="sxs-lookup"><span data-stu-id="8d91c-113">**Client NuGet packages**</span></span> | <span data-ttu-id="8d91c-114">[Microsoft. AspNet. :::no-loc(SignalR)::: . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-114">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="8d91c-115">[Microsoft. AspNet. :::no-loc(SignalR)::: . JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-115">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="8d91c-116">[Microsoft. AspNetCore. :::no-loc(SignalR)::: . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-116">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="8d91c-117">**JavaScript istemcisi NPM paketi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="8d91c-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="8d91c-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="8d91c-119">**Java istemcisi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-119">**Java client**</span></span> | <span data-ttu-id="8d91c-120">[GitHub deposu](https://github.com/:::no-loc(SignalR):::/java-client) (kullanım dışı)</span><span class="sxs-lookup"><span data-stu-id="8d91c-120">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="8d91c-121">Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="8d91c-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="8d91c-122">**Sunucu uygulaması türü**</span><span class="sxs-lookup"><span data-stu-id="8d91c-122">**Server app type**</span></span> | <span data-ttu-id="8d91c-123">ASP.NET (System. Web) veya OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="8d91c-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="8d91c-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d91c-124">ASP.NET Core</span></span> |
| <span data-ttu-id="8d91c-125">**Desteklenen sunucu platformları**</span><span class="sxs-lookup"><span data-stu-id="8d91c-125">**Supported server platforms**</span></span> | <span data-ttu-id="8d91c-126">.NET Framework 4,5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8d91c-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="8d91c-127">.NET Core 3,0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8d91c-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="8d91c-128">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8d91c-128">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="8d91c-129">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8d91c-129">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="8d91c-130">**Sunucu NuGet paketi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-130">**Server NuGet package**</span></span> | <span data-ttu-id="8d91c-131">[Microsoft. AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-131">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="8d91c-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="8d91c-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="8d91c-133">[Microsoft. AspNetCore.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-133">[Microsoft.AspNetCore.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span></span> <span data-ttu-id="8d91c-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="8d91c-134">(.NET Framework)</span></span> |
| <span data-ttu-id="8d91c-135">**İstemci NuGet paketleri**</span><span class="sxs-lookup"><span data-stu-id="8d91c-135">**Client NuGet packages**</span></span> | <span data-ttu-id="8d91c-136">[Microsoft. AspNet. :::no-loc(SignalR)::: . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-136">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="8d91c-137">[Microsoft. AspNet. :::no-loc(SignalR)::: . JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-137">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="8d91c-138">[Microsoft. AspNetCore. :::no-loc(SignalR)::: . İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="8d91c-138">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="8d91c-139">**JavaScript istemcisi NPM paketi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="8d91c-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="8d91c-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="8d91c-141">**Java istemcisi**</span><span class="sxs-lookup"><span data-stu-id="8d91c-141">**Java client**</span></span> | <span data-ttu-id="8d91c-142">[GitHub deposu](https://github.com/:::no-loc(SignalR):::/java-client) (kullanım dışı)</span><span class="sxs-lookup"><span data-stu-id="8d91c-142">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="8d91c-143">Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="8d91c-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="8d91c-144">**Sunucu uygulaması türü**</span><span class="sxs-lookup"><span data-stu-id="8d91c-144">**Server app type**</span></span> | <span data-ttu-id="8d91c-145">ASP.NET (System. Web) veya OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="8d91c-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="8d91c-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d91c-146">ASP.NET Core</span></span> |
| <span data-ttu-id="8d91c-147">**Desteklenen sunucu platformları**</span><span class="sxs-lookup"><span data-stu-id="8d91c-147">**Supported server platforms**</span></span> | <span data-ttu-id="8d91c-148">.NET Framework 4,5 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8d91c-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="8d91c-149">.NET Framework 4.6.1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8d91c-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="8d91c-150">.NET Core 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8d91c-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="8d91c-151">Özellik farklılıkları</span><span class="sxs-lookup"><span data-stu-id="8d91c-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="8d91c-152">Otomatik yeniden bağlantılar</span><span class="sxs-lookup"><span data-stu-id="8d91c-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d91c-153">ASP.NET içinde :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="8d91c-153">In ASP.NET :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="8d91c-154">Varsayılan olarak, :::no-loc(SignalR)::: bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-154">By default, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="8d91c-155">ASP.NET Core :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="8d91c-155">In ASP.NET Core :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="8d91c-156">Otomatik yeniden bağlantılar hem [.net istemcisiyle](xref:signalr/dotnet-client#automatically-reconnect) hem de [JavaScript istemcisiyle](xref:signalr/javascript-client#automatically-reconnect)birlikte kabul edilir:</span><span class="sxs-lookup"><span data-stu-id="8d91c-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

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

<span data-ttu-id="8d91c-157">ASP.NET Core 3,0 ' dan önce :::no-loc(SignalR)::: Otomatik yeniden bağlantılar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="8d91c-157">Prior to ASP.NET Core 3.0, :::no-loc(SignalR)::: doesn't support automatic reconnects.</span></span> <span data-ttu-id="8d91c-158">İstemcinin bağlantısı kesilirse, kullanıcının yeniden bağlanmak için açık olarak yeni bir bağlantı başlatması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="8d91c-159">ASP.NET :::no-loc(SignalR)::: ' de, :::no-loc(SignalR)::: bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-159">In ASP.NET :::no-loc(SignalR):::, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="8d91c-160">Protokol desteği</span><span class="sxs-lookup"><span data-stu-id="8d91c-160">Protocol support</span></span>

<span data-ttu-id="8d91c-161">ASP.NET Core :::no-loc(SignalR)::: , JSON 'ı ve [MessagePack](xref:signalr/messagepackhubprotocol)tabanlı yeni bir ikili protokolü destekler.</span><span class="sxs-lookup"><span data-stu-id="8d91c-161">ASP.NET Core :::no-loc(SignalR)::: supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="8d91c-162">Ek olarak özel protokoller de oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="8d91c-163">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="8d91c-163">Transports</span></span>

<span data-ttu-id="8d91c-164">ASP.NET Core içinde süresiz çerçeve taşıması desteklenmez :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-164">The Forever Frame transport isn't supported in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="8d91c-165">Sunucu farkları</span><span class="sxs-lookup"><span data-stu-id="8d91c-165">Differences on the server</span></span>

<span data-ttu-id="8d91c-166">ASP.NET Core :::no-loc(SignalR)::: sunucu tarafı kitaplıkları, hem hem de MVC projeleri için **ASP.NET Core Web uygulaması** şablonunda kullanılan [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)'e dahildir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-166">The ASP.NET Core :::no-loc(SignalR)::: server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both :::no-loc(Razor)::: and MVC projects.</span></span>

<span data-ttu-id="8d91c-167">ASP.NET Core :::no-loc(SignalR)::: bir ASP.NET Core ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="8d91c-167">ASP.NET Core :::no-loc(SignalR)::: is an ASP.NET Core middleware.</span></span> <span data-ttu-id="8d91c-168">' De çağırarak yapılandırılması gerekir <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d91c-169">Yönlendirmeyi yapılandırmak için, yolları yöntemindeki Yöntem çağrısı içindeki hublara eşleyin <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8d91c-170">Yönlendirmeyi yapılandırmak için, yolları yöntemindeki Yöntem çağrısı içindeki hublara eşleyin <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.Use:::no-loc(SignalR):::(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="8d91c-171">Yapışkan oturumlar</span><span class="sxs-lookup"><span data-stu-id="8d91c-171">Sticky sessions</span></span>

<span data-ttu-id="8d91c-172">ASP.NET için genişleme modeli, :::no-loc(SignalR)::: istemcilerin gruptaki herhangi bir sunucuya yeniden bağlanmasına ve ileti gönderemesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-172">The scaleout model for ASP.NET :::no-loc(SignalR)::: allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="8d91c-173">ASP.NET Core :::no-loc(SignalR)::: , istemcinin bağlantı süresince aynı sunucu ile etkileşimde olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-173">In ASP.NET Core :::no-loc(SignalR):::, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="8d91c-174">Redsıs kullanarak genişleme için, bu, yapışkan oturumların gerekli olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="8d91c-175">[Azure :::no-loc(SignalR)::: hizmeti](/azure/azure-signalr/)'ni kullanarak genişleme için, hizmet istemcilerle bağlantıları işlediği için yapışkan oturumlar gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-175">For scaleout using [Azure :::no-loc(SignalR)::: Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="8d91c-176">Bağlantı başına tek hub</span><span class="sxs-lookup"><span data-stu-id="8d91c-176">Single hub per connection</span></span>

<span data-ttu-id="8d91c-177">ASP.NET Core :::no-loc(SignalR)::: , bağlantı modeli basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-177">In ASP.NET Core :::no-loc(SignalR):::, the connection model has been simplified.</span></span> <span data-ttu-id="8d91c-178">Bağlantılar, birden çok hub 'a erişimi paylaşmak için kullanılan tek bir bağlantı yerine doğrudan tek bir hub 'a yapılır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="8d91c-179">Akış</span><span class="sxs-lookup"><span data-stu-id="8d91c-179">Streaming</span></span>

<span data-ttu-id="8d91c-180">ASP.NET Core :::no-loc(SignalR)::: artık hub 'dan istemciye [veri akışını](xref:signalr/streaming) desteklemektedir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-180">ASP.NET Core :::no-loc(SignalR)::: now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="8d91c-181">Durum</span><span class="sxs-lookup"><span data-stu-id="8d91c-181">State</span></span>

<span data-ttu-id="8d91c-182">İstemciler ve merkez arasında rastgele durum geçirme özelliği (genellikle denir `HubState` ) kaldırılmıştır ve ilerleme durumu iletileri için destek de sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d91c-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="8d91c-183">Şu anda hub proxy 'lerinin karşılığı yok.</span><span class="sxs-lookup"><span data-stu-id="8d91c-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="8d91c-184">PersistentConnection kaldırma</span><span class="sxs-lookup"><span data-stu-id="8d91c-184">PersistentConnection removal</span></span>

<span data-ttu-id="8d91c-185">ASP.NET Core :::no-loc(SignalR)::: , [Persistentconnection](/previous-versions/aspnet/jj919047(v=vs.118)) sınıfı kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-185">In ASP.NET Core :::no-loc(SignalR):::, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="8d91c-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="8d91c-186">GlobalHost</span></span>

<span data-ttu-id="8d91c-187">ASP.NET Core çerçeveye yerleşik bağımlılık ekleme (dı) vardır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="8d91c-188">Hizmetler, [Hubcontext](xref:signalr/hubcontext)'e erışmek için dı kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="8d91c-189">`GlobalHost`ASP.NET içinde kullanılacak nesne :::no-loc(SignalR)::: `HubContext` ASP.NET Core yok :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-189">The `GlobalHost` object that is used in ASP.NET :::no-loc(SignalR)::: to get a `HubContext` doesn't exist in ASP.NET Core :::no-loc(SignalR):::.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="8d91c-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="8d91c-190">HubPipeline</span></span>

<span data-ttu-id="8d91c-191">ASP.NET Core :::no-loc(SignalR)::: modüller için desteğe sahip değildir `HubPipeline` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-191">ASP.NET Core :::no-loc(SignalR)::: doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="8d91c-192">İstemci farkları</span><span class="sxs-lookup"><span data-stu-id="8d91c-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="8d91c-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="8d91c-193">TypeScript</span></span>

<span data-ttu-id="8d91c-194">ASP.NET Core :::no-loc(SignalR)::: Istemcisi [TypeScript](https://www.typescriptlang.org/)dilinde yazılır.</span><span class="sxs-lookup"><span data-stu-id="8d91c-194">The ASP.NET Core :::no-loc(SignalR)::: client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="8d91c-195">[JavaScript istemcisini](xref:signalr/javascript-client)kullanırken JavaScript veya TypeScript ile yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8d91c-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="8d91c-196">JavaScript istemcisi NPM 'de barındırılıyor</span><span class="sxs-lookup"><span data-stu-id="8d91c-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d91c-197">ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi.</span><span class="sxs-lookup"><span data-stu-id="8d91c-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="8d91c-198">ASP.NET Core sürümlerinde [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM paketi Javascript kitaplıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="8d91c-199">Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="8d91c-200">NPM paketini edinmek ve yüklemek için NPM kullanın `@microsoft/signalr` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8d91c-201">ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi.</span><span class="sxs-lookup"><span data-stu-id="8d91c-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="8d91c-202">ASP.NET Core sürümlerinde [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM paketi Javascript kitaplıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="8d91c-203">Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="8d91c-204">NPM paketini edinmek ve yüklemek için NPM kullanın `@aspnet/signalr` .</span><span class="sxs-lookup"><span data-stu-id="8d91c-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="8d91c-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="8d91c-205">jQuery</span></span>

<span data-ttu-id="8d91c-206">JQuery üzerindeki bağımlılık kaldırılmıştır, ancak projeler jQuery kullanmaya devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="8d91c-207">Internet Explorer desteği</span><span class="sxs-lookup"><span data-stu-id="8d91c-207">Internet Explorer support</span></span>

<span data-ttu-id="8d91c-208">ASP.NET Core Microsoft :::no-loc(SignalR)::: Internet Explorer 11 veya üstünü destekler, ancak ASP.net :::no-loc(SignalR)::: Microsoft Internet Explorer 8 veya üstünü destekler.</span><span class="sxs-lookup"><span data-stu-id="8d91c-208">ASP.NET Core :::no-loc(SignalR)::: supports Microsoft Internet Explorer 11 or later, whereas ASP.NET :::no-loc(SignalR)::: supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="8d91c-209">Tarayıcı desteği hakkında daha fazla bilgi için [desteklenen platformlarda](xref:signalr/supported-platforms#javascript-client)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="8d91c-210">JavaScript istemci yöntemi sözdizimi</span><span class="sxs-lookup"><span data-stu-id="8d91c-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d91c-211">JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-211">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="8d91c-212">Nesnesini kullanmak yerine `$connection` , [Hubconnectionbuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8d91c-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="8d91c-213">Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@microsoft/signalr/HubConnection#on) metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d91c-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8d91c-214">JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-214">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="8d91c-215">Nesnesini kullanmak yerine `$connection` , [Hubconnectionbuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8d91c-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="8d91c-216">Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@aspnet/signalr/HubConnection#on) metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d91c-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="8d91c-217">İstemci yöntemini oluşturduktan sonra hub bağlantısını başlatın.</span><span class="sxs-lookup"><span data-stu-id="8d91c-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="8d91c-218">Günlüğe kaydetmek veya hataları işlemek için bir [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodunu zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="8d91c-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="8d91c-219">Hub proxy 'leri</span><span class="sxs-lookup"><span data-stu-id="8d91c-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d91c-220">Hub proxy 'leri artık otomatik olarak oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="8d91c-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="8d91c-221">Bunun yerine, yöntem adı [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8d91c-222">Hub proxy 'leri artık otomatik olarak oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="8d91c-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="8d91c-223">Bunun yerine, yöntem adı [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="8d91c-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="8d91c-224">.NET ve diğer istemciler</span><span class="sxs-lookup"><span data-stu-id="8d91c-224">.NET and other clients</span></span>

<span data-ttu-id="8d91c-225">[Microsoft. AspNetCore. :::no-loc(SignalR)::: . İstemci](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) NuGet paketi, ASP.NET Core için .NET istemci kitaplıklarını içerir :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="8d91c-225">The [Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) NuGet package contains the .NET client libraries for ASP.NET Core :::no-loc(SignalR):::.</span></span>

<span data-ttu-id="8d91c-226"><xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>Bir hub bağlantısının örneğini oluşturmak ve derlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d91c-226">Use the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="8d91c-227">Ölçek Genişletme farklılıkları</span><span class="sxs-lookup"><span data-stu-id="8d91c-227">Scaleout differences</span></span>

<span data-ttu-id="8d91c-228">ASP.NET :::no-loc(SignalR)::: SQL Server ve redsıs destekler.</span><span class="sxs-lookup"><span data-stu-id="8d91c-228">ASP.NET :::no-loc(SignalR)::: supports SQL Server and Redis.</span></span> <span data-ttu-id="8d91c-229">ASP.NET Core :::no-loc(SignalR)::: Azure :::no-loc(SignalR)::: hizmeti ve redsıs 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="8d91c-229">ASP.NET Core :::no-loc(SignalR)::: supports Azure :::no-loc(SignalR)::: Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="8d91c-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8d91c-230">ASP.NET</span></span>

* [<span data-ttu-id="8d91c-231">:::no-loc(SignalR)::: Azure Service Bus ile ölçeği genişletme</span><span class="sxs-lookup"><span data-stu-id="8d91c-231">:::no-loc(SignalR)::: scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="8d91c-232">:::no-loc(SignalR)::: Redsıs ile ölçek genişletme</span><span class="sxs-lookup"><span data-stu-id="8d91c-232">:::no-loc(SignalR)::: scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="8d91c-233">:::no-loc(SignalR)::: SQL Server ile ölçeği genişletme</span><span class="sxs-lookup"><span data-stu-id="8d91c-233">:::no-loc(SignalR)::: scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="8d91c-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d91c-234">ASP.NET Core</span></span>

* [<span data-ttu-id="8d91c-235">Azure :::no-loc(SignalR)::: hizmeti</span><span class="sxs-lookup"><span data-stu-id="8d91c-235">Azure :::no-loc(SignalR)::: Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="8d91c-236">Redsıs geri düzlemi</span><span class="sxs-lookup"><span data-stu-id="8d91c-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="8d91c-237">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8d91c-237">Additional resources</span></span>

* [<span data-ttu-id="8d91c-238">Merkezler</span><span class="sxs-lookup"><span data-stu-id="8d91c-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8d91c-239">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="8d91c-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="8d91c-240">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="8d91c-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="8d91c-241">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="8d91c-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
