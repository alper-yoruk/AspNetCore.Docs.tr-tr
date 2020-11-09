---
title: ASP.NET Core için istemci IP SafeList
author: damienbod
description: Uzak IP adreslerini onaylanan IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri yazmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: dfc134b97bb0976bc682a53d536cd27785550c7d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059669"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="f5a3b-103">ASP.NET Core için istemci IP SafeList</span><span class="sxs-lookup"><span data-stu-id="f5a3b-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="f5a3b-104">By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f5a3b-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="f5a3b-105">Bu makalede bir ASP.NET Core uygulamasında bir IP adresi SafeList (izin verilenler listesi olarak da bilinir) uygulamanın üç yolu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="f5a3b-106">Eşlik eden bir örnek uygulama, üç yaklaşımdan oluşan tüm örnekleri gösterir</span><span class="sxs-lookup"><span data-stu-id="f5a3b-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="f5a3b-107">Şunu kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-107">You can use:</span></span>

* <span data-ttu-id="f5a3b-108">Her isteğin uzak IP adresini denetlemek için ara yazılım.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="f5a3b-109">MVC eylemi belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için filtreler.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="f5a3b-110">Razor Sayfa isteklerinin uzak IP adresini denetlemek için sayfalar filtreler Razor .</span><span class="sxs-lookup"><span data-stu-id="f5a3b-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="f5a3b-111">Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="f5a3b-112">Ara yazılım veya filtre:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-112">The middleware or filter:</span></span>

* <span data-ttu-id="f5a3b-113">Dizeyi bir dizi olarak ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="f5a3b-114">Dizide uzak IP adresinin bulunup bulunmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="f5a3b-115">Dizi IP adresini içeriyorsa erişime izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="f5a3b-116">Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="f5a3b-117">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5a3b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="f5a3b-118">IP adresi SafeList</span><span class="sxs-lookup"><span data-stu-id="f5a3b-118">IP address safelist</span></span>

<span data-ttu-id="f5a3b-119">Örnek uygulamada, IP adresi SafeList şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="f5a3b-120">`AdminSafeList`Dosyadaki özelliği tarafından tanımlanır *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5a3b-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="f5a3b-121">Hem [Internet Protokolü sürüm 4 (IPv4)](https://wikipedia.org/wiki/IPv4) hem de [İnternet Protokolü sürüm 6 (IPv6)](https://wikipedia.org/wiki/IPv6) adresleri içerebilen, noktalı virgülle ayrılmış bir dize.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="f5a3b-122">Yukarıdaki örnekte, ve ' nin IPv4 adreslerine ve `127.0.0.1` `192.168.1.5` IPv6 Geridöngü adresine `::1` (için sıkıştırılmış biçim `0:0:0:0:0:0:0:1` ) izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="f5a3b-123">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="f5a3b-123">Middleware</span></span>

<span data-ttu-id="f5a3b-124">`Startup.Configure`Yöntemi, `AdminSafeListMiddleware` uygulamanın istek ardışık düzenine özel ara yazılım türünü ekler.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="f5a3b-125">SafeList, .NET Core yapılandırma sağlayıcısıyla alınır ve bir oluşturucu parametresi olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="f5a3b-126">Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="f5a3b-127">Uzak IP adresi bulunamazsa, ara yazılım HTTP 403 Yasak değerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="f5a3b-128">Bu doğrulama işlemi HTTP GET istekleri için atlanır.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="f5a3b-129">Eylem filtresi</span><span class="sxs-lookup"><span data-stu-id="f5a3b-129">Action filter</span></span>

<span data-ttu-id="f5a3b-130">Belirli MVC denetleyicileri veya eylem yöntemleri için güvenli liste temelli erişim denetimi istiyorsanız, bir eylem filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="f5a3b-131">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="f5a3b-132">İçinde `Startup.ConfigureServices` , eylem FILTRESINI MVC filtreleri koleksiyonuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="f5a3b-133">Aşağıdaki örnekte, bir `ClientIpCheckActionFilter` eylem filtresi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="f5a3b-134">Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="f5a3b-135">Eylem filtresi daha sonra [[Servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) özniteliğiyle bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="f5a3b-136">Örnek uygulamada, eylem filtresi denetleyicinin `Get` eylem yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="f5a3b-137">Uygulamayı şunu göndererek test ettiğinizde:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-137">When you test the app by sending:</span></span>

* <span data-ttu-id="f5a3b-138">HTTP GET isteği, `[ServiceFilter]` özniteliği ISTEMCI IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="f5a3b-139">Eylem yöntemine erişime izin veriliyorsa `Get` , aşağıdaki konsol çıkışının bir çeşitlemesi eylem filtresi ve eylem yöntemi tarafından üretilir:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="f5a3b-140">GET dışında bir HTTP istek fiili, `AdminSafeListMiddleware` Ara yazılım ISTEMCI IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="f5a3b-141">Razor Sayfa filtresi</span><span class="sxs-lookup"><span data-stu-id="f5a3b-141">Razor Pages filter</span></span>

<span data-ttu-id="f5a3b-142">Bir sayfalar uygulaması için güvenli liste temelli erişim denetimi istiyorsanız Razor , bir Razor sayfa filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="f5a3b-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="f5a3b-144">İçinde `Startup.ConfigureServices` , Razor MVC filtreleri koleksiyonuna ekleyerek sayfa filtresini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="f5a3b-145">Aşağıdaki örnekte, bir `ClientIpCheckPageFilter` Razor sayfa filtresi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="f5a3b-146">Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="f5a3b-147">Örnek uygulamanın *Dizin* Razor sayfası istendiğinde, Razor sayfa filtresi istemci IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f5a3b-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="f5a3b-148">Filtre, aşağıdaki konsol çıkışının bir çeşidini üretir:</span><span class="sxs-lookup"><span data-stu-id="f5a3b-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="f5a3b-149">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f5a3b-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f5a3b-150">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="f5a3b-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
