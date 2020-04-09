---
title: ASP.NET Core için istemci IP güvenli listesi
author: damienbod
description: Uzak IP adreslerini onaylanmış IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri nasıl yazılanın.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471809"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="f743e-103">ASP.NET Core için istemci IP güvenli listesi</span><span class="sxs-lookup"><span data-stu-id="f743e-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="f743e-104">Yazar: [Damien Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f743e-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="f743e-105">Bu makalede, bir ASP.NET Core uygulamasında bir IP adresi güvenli listesi (izin listesi olarak da bilinir) uygulamanın üç yolu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f743e-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="f743e-106">Eşlik eden bir örnek uygulama her üç yaklaşımı da gösterir.</span><span class="sxs-lookup"><span data-stu-id="f743e-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="f743e-107">Şunu kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f743e-107">You can use:</span></span>

* <span data-ttu-id="f743e-108">Middleware her isteğin uzak IP adresini kontrol etmek için.</span><span class="sxs-lookup"><span data-stu-id="f743e-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="f743e-109">MVC eylem filtreleri belirli denetleyicileri veya eylem yöntemleri için isteklerin uzak IP adresini kontrol etmek için.</span><span class="sxs-lookup"><span data-stu-id="f743e-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="f743e-110">Razor Sayfaları, Razor sayfaları için isteklerin uzak IP adresini kontrol etmek için filtreler.</span><span class="sxs-lookup"><span data-stu-id="f743e-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="f743e-111">Her durumda, onaylı istemci IP adreslerini içeren bir dize bir uygulama ayarında depolanır.</span><span class="sxs-lookup"><span data-stu-id="f743e-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="f743e-112">Ara yazılım veya filtre:</span><span class="sxs-lookup"><span data-stu-id="f743e-112">The middleware or filter:</span></span>

* <span data-ttu-id="f743e-113">Dizeyi bir diziye ayrışdırır.</span><span class="sxs-lookup"><span data-stu-id="f743e-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="f743e-114">Dizide uzak IP adresinin olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="f743e-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="f743e-115">Dizi IP adresini içeriyorsa erişime izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f743e-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="f743e-116">Aksi takdirde, bir HTTP 403 Yasak durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f743e-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="f743e-117">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f743e-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="f743e-118">IP adresi güvenli listesi</span><span class="sxs-lookup"><span data-stu-id="f743e-118">IP address safelist</span></span>

<span data-ttu-id="f743e-119">Örnek uygulamada IP adresi güvenli listesi:</span><span class="sxs-lookup"><span data-stu-id="f743e-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="f743e-120">`AdminSafeList` *appsettings.json* dosyasındaki özellik tarafından tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f743e-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="f743e-121">Hem [Internet Protokolü sürüm 4 (IPv4)](https://wikipedia.org/wiki/IPv4) hem de Internet Protocol sürüm 6 [(IPv6)](https://wikipedia.org/wiki/IPv6) adreslerini içerebilecek yarı sütunlu dize.</span><span class="sxs-lookup"><span data-stu-id="f743e-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="f743e-122">Önceki örnekte, IPv4 adresleri `127.0.0.1` ve `192.168.1.5` IPv6 döngü adresi `::1` (sıkıştırılmış biçimi `0:0:0:0:0:0:0:1`için) izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f743e-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="f743e-123">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="f743e-123">Middleware</span></span>

<span data-ttu-id="f743e-124">Yöntem, `Startup.Configure` uygulamanın `AdminSafeListMiddleware` istek ardışık hattına özel ara yazılım türünü ekler.</span><span class="sxs-lookup"><span data-stu-id="f743e-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="f743e-125">Safelist .NET Core yapılandırma sağlayıcısı ile alınır ve bir oluşturucu parametresi olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f743e-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="f743e-126">Ara yazılım dizeyi bir diziye dönüştürür ve dizideki uzak IP adresini arar.</span><span class="sxs-lookup"><span data-stu-id="f743e-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="f743e-127">Uzak IP adresi bulunamazsa, ara yazılım HTTP 403 Forbidden'ı döndürür.</span><span class="sxs-lookup"><span data-stu-id="f743e-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="f743e-128">Bu doğrulama işlemi HTTP GET istekleri için atlanır.</span><span class="sxs-lookup"><span data-stu-id="f743e-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="f743e-129">İşlem filtresi</span><span class="sxs-lookup"><span data-stu-id="f743e-129">Action filter</span></span>

<span data-ttu-id="f743e-130">Belirli MVC denetleyicileri veya eylem yöntemleri için safelist tabanlı erişim denetimi istiyorsanız, bir eylem filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f743e-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="f743e-131">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f743e-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="f743e-132">Içinde `Startup.ConfigureServices`, MVC filtreleri koleksiyonuna eylem filtresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f743e-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="f743e-133">Aşağıdaki örnekte, `ClientIpCheckActionFilter` bir eylem filtresi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f743e-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="f743e-134">Bir safelist ve bir konsol logger örneği oluşturucu parametreleri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f743e-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="f743e-135">Eylem filtresi daha sonra [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) özniteliği ile bir denetleyici veya eylem yöntemi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="f743e-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="f743e-136">Örnek uygulamada, eylem filtresi denetleyicinin `Get` eylem yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f743e-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="f743e-137">Uygulamayı aşağıdaki leri göndererek test ettiğinizde:</span><span class="sxs-lookup"><span data-stu-id="f743e-137">When you test the app by sending:</span></span>

* <span data-ttu-id="f743e-138">Bir HTTP GET `[ServiceFilter]` isteği, öznitelik istemci IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f743e-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="f743e-139">`Get` Eylem yöntemine erişime izin verilirse, eylem filtresi ve eylem yöntemi tarafından aşağıdaki konsol çıktısının bir varyasyonu üretilir:</span><span class="sxs-lookup"><span data-stu-id="f743e-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="f743e-140">GET dışında bir HTTP istek `AdminSafeListMiddleware` fiili, middleware istemci IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f743e-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="f743e-141">Jilet Sayfaları filtresi</span><span class="sxs-lookup"><span data-stu-id="f743e-141">Razor Pages filter</span></span>

<span data-ttu-id="f743e-142">Bir Razor Pages uygulaması için güvenli liste tabanlı erişim denetimi istiyorsanız, Bir Jilet Sayfaları filtresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f743e-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="f743e-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f743e-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="f743e-144">Içinde `Startup.ConfigureServices`, MVC filtreleri koleksiyonuna ekleyerek Razor Pages filtresi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f743e-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="f743e-145">Aşağıdaki örnekte, `ClientIpCheckPageFilter` bir Jilet Sayfaları filtresi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f743e-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="f743e-146">Bir safelist ve bir konsol logger örneği oluşturucu parametreleri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f743e-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="f743e-147">Örnek uygulamanın *Index* Razor sayfası istendiğinde, Razor Pages filtresi istemci IP adresini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f743e-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="f743e-148">Filtre, aşağıdaki konsol çıktısının bir varyasyonunu üretir:</span><span class="sxs-lookup"><span data-stu-id="f743e-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="f743e-149">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f743e-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f743e-150">İşlem filtreleri</span><span class="sxs-lookup"><span data-stu-id="f743e-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
