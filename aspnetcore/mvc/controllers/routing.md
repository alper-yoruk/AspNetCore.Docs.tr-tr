---
title: ASP.NET Core'daki eylemleri denetleyiciye yönlendirme
author: rick-anderson
description: Core MVCASP.NET gelen isteklerin URL'lerini eşleştirmek ve bunları eylemlerle eşlemek için Yönlendirme Middleware'i nasıl kullandığını öğrenin.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 74afd0a076ca8bd753000f547ef0a26308e8a884
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123498"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="393ab-103">ASP.NET Core'daki eylemleri denetleyiciye yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="393ab-104">Ryan [Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="393ab-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="393ab-105">ASP.NET Core denetleyicileri gelen isteklerin URL'leri eşlemek ve [eylemlerle](#action)eşlemek için Yönlendirme [ara ware](xref:fundamentals/middleware/index) kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="393ab-106">Rotaşablonları:</span><span class="sxs-lookup"><span data-stu-id="393ab-106">Routes templates:</span></span>

* <span data-ttu-id="393ab-107">Başlangıç kodu veya öznitelikleri tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="393ab-108">URL yollarının [eylemlerle](#action)nasıl eşleştiğini açıklayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="393ab-109">Bağlantılar için URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="393ab-110">Oluşturulan bağlantılar genellikle yanıtlar olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="393ab-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="393ab-111">Eylemler geleneksel [olarak yönlendirilir](#cr) veya [öznitelik-yönlendirilir.](#ar)</span><span class="sxs-lookup"><span data-stu-id="393ab-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="393ab-112">Denetleyiciye veya [eyleme](#action) bir rota yerleştirmek, rotayı öznitelik yönlendiren yapar.</span><span class="sxs-lookup"><span data-stu-id="393ab-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="393ab-113">Daha fazla bilgi için [Karışık yönlendirmeye](#routing-mixed-ref-label) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="393ab-114">Bu belge:</span><span class="sxs-lookup"><span data-stu-id="393ab-114">This document:</span></span>

* <span data-ttu-id="393ab-115">MVC ve yönlendirme arasındaki etkileşimleri açıklar:</span><span class="sxs-lookup"><span data-stu-id="393ab-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="393ab-116">Tipik MVC uygulamaları yönlendirme özelliklerinden nasıl yararlanıyor?</span><span class="sxs-lookup"><span data-stu-id="393ab-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="393ab-117">Her ikisini de kapsar:</span><span class="sxs-lookup"><span data-stu-id="393ab-117">Covers both:</span></span>
    * <span data-ttu-id="393ab-118">[Geleneksel yönlendirme](#cr) genellikle denetleyicileri ve görünümleri ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="393ab-119">REST API'leri ile kullanılan *öznitelik* yönlendirmesi.</span><span class="sxs-lookup"><span data-stu-id="393ab-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="393ab-120">Öncelikle REST API'leri yönlendirmeyle ilgileniyorsanız, REST [API'leri için Öznitelik yönlendirmesine](#ar) atlayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="393ab-121">Gelişmiş yönlendirme ayrıntıları için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="393ab-122">ASP.NET Core 3.0'a eklenen ve bitiş noktası yönlendirmesi olarak adlandırılan varsayılan yönlendirme sistemini ifade eder.</span><span class="sxs-lookup"><span data-stu-id="393ab-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="393ab-123">Uyumluluk amacıyla yönlendirmenin önceki sürümüne sahip denetleyicileri kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="393ab-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="393ab-124">Talimatlar için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="393ab-125">Eski yönlendirme sistemindereferans materyali için [bu belgenin 2.2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="393ab-126">Geleneksel rotayı ayarlama</span><span class="sxs-lookup"><span data-stu-id="393ab-126">Set up conventional route</span></span>

<span data-ttu-id="393ab-127">`Startup.Configure`genellikle [geleneksel yönlendirme](#crd)kullanırken aşağıdakilere benzer bir kodvardır:</span><span class="sxs-lookup"><span data-stu-id="393ab-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="393ab-128">Çağrının içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , tek bir rota oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="393ab-129">Tek rota rota `default` olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-129">The single route is named `default` route.</span></span> <span data-ttu-id="393ab-130">Denetleyicileri ve görünümleri olan uygulamaların çoğu, `default` rotaya benzer bir rota şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="393ab-131">REST API'ler [öznitelik yönlendirme](#ar)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="393ab-132">Rota şablonu: `"{controller=Home}/{action=Index}/{id?}"`</span><span class="sxs-lookup"><span data-stu-id="393ab-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="393ab-133">Url yolunu eşleştirin`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="393ab-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="393ab-134">Yolu belirterek `{ controller = Products, action = Details, id = 5 }` rota değerlerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="393ab-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="393ab-135">Uygulamanın adında `ProductsController` bir denetleyicisi ve bir `Details` eylemi varsa, rota değerlerinin çıkarılması eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="393ab-136">`/Products/Details/5`modeli `id = 5` `id` parametreyi ' ye ayarlamak için `5`değerini bağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="393ab-137">Daha fazla ayrıntı için [Model Bağlama'ya](xref:mvc/models/model-binding) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="393ab-138">`{controller=Home}`varsayılan `controller` `Home` olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="393ab-139">`{action=Index}`varsayılan `action` `Index` olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="393ab-140">Karakter `?` isteğe `id` bağlı olarak `{id?}` tanımlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="393ab-141">Varsayılan ve isteğe bağlı rota parametrelerinin eşleşme için URL yolunda bulunması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="393ab-142">Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="393ab-143">URL yolu `/`yla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="393ab-144">Rota değerlerini `{ controller = Home, action = Index }`üretir.</span><span class="sxs-lookup"><span data-stu-id="393ab-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="393ab-145">Varsayılan değerler `controller` `action` için değerler ve kullanım.</span><span class="sxs-lookup"><span data-stu-id="393ab-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="393ab-146">`id`URL yolunda karşılık gelen bir segment olmadığından bir değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="393ab-147">`/`yalnızca bir `HomeController` ve `Index` eylem varsa eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="393ab-148">Önceki denetleyici tanımı ve yol şablonu `HomeController.Index` kullanılarak, eylem aşağıdaki URL yolları için çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="393ab-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="393ab-149">URL yolu, `/` rota şablonu varsayılan `Home` denetleyicilerini ve `Index` eylemi kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="393ab-150">URL yolu, `/Home` rota şablonu varsayılan `Index` eylemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="393ab-151">Kolaylık yöntemi: <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*></span><span class="sxs-lookup"><span data-stu-id="393ab-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="393ab-152">Değiştirir:</span><span class="sxs-lookup"><span data-stu-id="393ab-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="393ab-153"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> Yönlendirme, ara yazılım ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ara yazılım kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="393ab-154">Denetleyicileri kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="393ab-154">To use controllers:</span></span>
>
> * <span data-ttu-id="393ab-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> [Yönlendirilmiş](#ar) denetleyicileri eşlemek için içini `UseEndpoints` arayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="393ab-156">Geleneksel <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> [olarak yönlendirilmiş](#cr) denetleyicileri haritalamak için arayın veya.</span><span class="sxs-lookup"><span data-stu-id="393ab-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="393ab-157">Konvansiyonel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-157">Conventional routing</span></span>

<span data-ttu-id="393ab-158">Geleneksel yönlendirme denetleyicileri ve görünümleri ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="393ab-159">Rota: `default`</span><span class="sxs-lookup"><span data-stu-id="393ab-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="393ab-160">geleneksel bir *yönlendirme*örneğidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="393ab-161">URL yolları için bir *kural* oluşturmuştur çünkü *geleneksel yönlendirme* denir:</span><span class="sxs-lookup"><span data-stu-id="393ab-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="393ab-162">İlk yol kesimi, `{controller=Home}`denetleyici adı eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="393ab-163">İkinci bölüm, `{action=Index}` [eylem](#action) adı eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="393ab-164">Üçüncü segment, `{id?}` isteğe bağlı `id`olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="393ab-165">In `?` `{id?}` isteğe bağlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="393ab-166">`id`bir model varlık için eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="393ab-167">Bu `default` yolu kullanarak, URL yolu:</span><span class="sxs-lookup"><span data-stu-id="393ab-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="393ab-168">`/Products/List`eylem eharitalar. `ProductsController.List`</span><span class="sxs-lookup"><span data-stu-id="393ab-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="393ab-169">`/Blog/Article/17`haritaları `BlogController.Article` ve genellikle model parametreyi `id` 17'ye bağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="393ab-170">Bu haritalama:</span><span class="sxs-lookup"><span data-stu-id="393ab-170">This mapping:</span></span>

* <span data-ttu-id="393ab-171">Denetleyici ve [eylem](#action) adlarını temel alan **sadece.**</span><span class="sxs-lookup"><span data-stu-id="393ab-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="393ab-172">Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel almıyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="393ab-173">Varsayılan rota ile geleneksel yönlendirme kullanarak her eylem için yeni bir URL deseni ile gelmek zorunda kalmadan uygulama oluşturma sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="393ab-174">KUMANDALAR arasında URL'ler için tutarlılık olan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) tarzı eylemlere sahip bir uygulama için:</span><span class="sxs-lookup"><span data-stu-id="393ab-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="393ab-175">Kodu basitleştirmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-175">Helps simplify the code.</span></span>
* <span data-ttu-id="393ab-176">UI'yi daha öngörülebilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="393ab-177">Önceki `id` kod, rota şablonu tarafından isteğe bağlı olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="393ab-178">Eylemler, URL'nin bir parçası olarak sağlanan isteğe bağlı kimlik olmadan yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="393ab-179">Genellikle,`id` URL'den atlandığında:</span><span class="sxs-lookup"><span data-stu-id="393ab-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="393ab-180">`id`model bağlama `0` tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="393ab-181">Veritabanında eşleşen `id == 0`varlık bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="393ab-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="393ab-182">[Öznitelik yönlendirme,](#ar) kimliğin diğerleri için değil, bazı eylemler için gerekli olmasını sağlamak için ince taneli denetim sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="393ab-183">Sözleşmeye göre, belgeler doğru `id` kullanımda görünme olasılıkları gibi isteğe bağlı parametreleri içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="393ab-184">Çoğu uygulama, URL'lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="393ab-185">Varsayılan geleneksel `{controller=Home}/{action=Index}/{id?}`rota:</span><span class="sxs-lookup"><span data-stu-id="393ab-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="393ab-186">Temel ve açıklayıcı yönlendirme düzenini destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="393ab-187">UI tabanlı uygulamalar için yararlı bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="393ab-188">Birçok web Ara Birimi uygulaması için gereken tek rota şablonudur.</span><span class="sxs-lookup"><span data-stu-id="393ab-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="393ab-189">Daha büyük web kullanıcı arabirimi uygulamaları için, sık sık gereken tüm alanlar [kullanarak](#areas) başka bir rota.</span><span class="sxs-lookup"><span data-stu-id="393ab-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="393ab-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="393ab-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="393ab-191">Çağrıldıkları sıraya göre uç noktalarına otomatik olarak bir **sipariş** değeri atayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="393ab-192">Core 3.0 ve sonraki ASP.NET'da bitiş noktası yönlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="393ab-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="393ab-193">Güzergah kavramı yok.</span><span class="sxs-lookup"><span data-stu-id="393ab-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="393ab-194">Genişletilebilirliğin yürütülmesi için sipariş garantisi sağlamaz, tüm uç noktalar aynı anda işlenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="393ab-195">Yerleşik yönlendirme uygulamalarının <xref:Microsoft.AspNetCore.Routing.Route>istekleri nasıl eşleştirbildiğini görmek için [Günlüğe Kaydetme'yi](xref:fundamentals/logging/index) etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="393ab-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="393ab-196">[Öznitelik yönlendirme](#ar) süresonra bu belgede açıklanıyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="393ab-197">Birden fazla konvansiyonel rota</span><span class="sxs-lookup"><span data-stu-id="393ab-197">Multiple conventional routes</span></span>

<span data-ttu-id="393ab-198">Birden fazla [konvansiyonel rotalar](#cr) daha fazla <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>çağrı ekleyerek içine `UseEndpoints` eklenebilir ve .</span><span class="sxs-lookup"><span data-stu-id="393ab-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="393ab-199">Bunu yapmak, birden çok kuralın tanımlanmasına veya belirli bir [eyleme](#action)ayrılmış geleneksel yolların eklenmesine olanak tanır ( örneğin:</span><span class="sxs-lookup"><span data-stu-id="393ab-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="393ab-200">Önceki `blog` koddaki rota özel bir **geleneksel rotadır.**</span><span class="sxs-lookup"><span data-stu-id="393ab-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="393ab-201">Buna özel konvansiyonel rota denir, çünkü:</span><span class="sxs-lookup"><span data-stu-id="393ab-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="393ab-202">Bu [geleneksel yönlendirme](#cr)kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="393ab-203">Belirli bir [eyleme](#action)adanmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="393ab-204">Çünkü `controller` `action` ve rota şablonunda `"blog/{*article}"` parametre olarak görünmeyin:</span><span class="sxs-lookup"><span data-stu-id="393ab-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="393ab-205">Yalnızca varsayılan değerlere `{ controller = "Blog", action = "Article" }`sahip olabilirler.</span><span class="sxs-lookup"><span data-stu-id="393ab-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="393ab-206">Bu rota her zaman `BlogController.Article`eylem eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="393ab-207">`/Blog`, `/Blog/Article`ve `/Blog/{any-string}` blog rotasıyla eşleşen tek URL yollarıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="393ab-208">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="393ab-208">The preceding example:</span></span>

* <span data-ttu-id="393ab-209">`blog`önce ekilen rota, `default` maçlar için rotadan daha yüksek bir önceliğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="393ab-210">URL'nin bir parçası olarak bir makale adı olması nın tipik olduğu [Sümüklü böcek](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirmesi ve örneğidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="393ab-211">Core 3.0 ve daha sonra ASP.NET, yönlendirme şunları yapmaz:</span><span class="sxs-lookup"><span data-stu-id="393ab-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="393ab-212">Rota adı verilen bir kavram *tanımlayın.*</span><span class="sxs-lookup"><span data-stu-id="393ab-212">Define a concept called a *route*.</span></span> <span data-ttu-id="393ab-213">`UseRouting`ara yazılım boru hattına eşleşen rota ekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="393ab-214">Ara `UseRouting` yazılım, uygulamada tanımlanan uç nokta kümesine bakar ve isteğe bağlı olarak en iyi bitiş noktası eşleşmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="393ab-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="393ab-215">Gibi <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> genişletilebilirlik yürütme emri hakkında garanti <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>ler sağlayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="393ab-216">Yönlendirme yle ilgili referans materyali için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="393ab-217">Konvansiyonel yönlendirme düzeni</span><span class="sxs-lookup"><span data-stu-id="393ab-217">Conventional routing order</span></span>

<span data-ttu-id="393ab-218">Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyici nin bir birleşimi ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="393ab-219">Bu, geleneksel yolların çakıştığı servis taleplerini basitleştirmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="393ab-220"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>Çağrıldıkları sıraya <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>göre, ", ve <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> otomatik olarak uç noktalarına bir sipariş değeri atay.</span><span class="sxs-lookup"><span data-stu-id="393ab-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="393ab-221">Daha önce görünen bir rotadaki eşleşmelerin daha yüksek bir önceliği vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="393ab-222">Konvansiyonel yönlendirme isteðe baðlð±r.</span><span class="sxs-lookup"><span data-stu-id="393ab-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="393ab-223">Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik oldukları için daha erken yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="393ab-224">Gibi `{*article}` tüm rota parametrelerini yakalamak ile [özel geleneksel rotalar](#dcr) çok [açgözlü](xref:fundamentals/routing#greedy)bir rota yapabilirsiniz, diğer rotalar ile eşleşen olması amaçlanan URL'ler eşleşen anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="393ab-225">Açgözlü eşleşmeleri önlemek için daha sonra rota tablosuna açgözlü rotaları koyun.</span><span class="sxs-lookup"><span data-stu-id="393ab-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="393ab-226">Belirsiz eylemleri çözme</span><span class="sxs-lookup"><span data-stu-id="393ab-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="393ab-227">İki uç nokta yönlendirme ile eşleştiğinde, yönlendirme aşağıdakilerden birini yapmalıdır:</span><span class="sxs-lookup"><span data-stu-id="393ab-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="393ab-228">En iyi adayı seç.</span><span class="sxs-lookup"><span data-stu-id="393ab-228">Choose the best candidate.</span></span>
* <span data-ttu-id="393ab-229">Bir istisna at.</span><span class="sxs-lookup"><span data-stu-id="393ab-229">Throw an exception.</span></span>

<span data-ttu-id="393ab-230">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="393ab-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="393ab-231">Önceki denetleyici eşleşen iki eylemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="393ab-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="393ab-232">URL yolu`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="393ab-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="393ab-233">Veri `{ controller = Products33, action = Edit, id = 17 }`yolu .</span><span class="sxs-lookup"><span data-stu-id="393ab-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="393ab-234">Bu, MVC denetleyicileri için tipik bir desendir:</span><span class="sxs-lookup"><span data-stu-id="393ab-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="393ab-235">`Edit(int)`bir ürünü sağlamak için bir form görüntüler.</span><span class="sxs-lookup"><span data-stu-id="393ab-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="393ab-236">`Edit(int, Product)`deftere nakledilen formu işler.</span><span class="sxs-lookup"><span data-stu-id="393ab-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="393ab-237">Doğru rotayı çözmek için:</span><span class="sxs-lookup"><span data-stu-id="393ab-237">To resolve the correct route:</span></span>

* <span data-ttu-id="393ab-238">`Edit(int, Product)`istek bir HTTP `POST`olduğunda seçilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="393ab-239">`Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="393ab-240">`Edit(int)`genellikle üzerinden `GET`denir.</span><span class="sxs-lookup"><span data-stu-id="393ab-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="393ab-241">, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, yönlendirmeye sağlanır, böylece isteğin HTTP yöntemine göre seçim yapabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="393ab-242">Daha `HttpPostAttribute` `Edit(int, Product)` iyi bir `Edit(int)`maç yapar.</span><span class="sxs-lookup"><span data-stu-id="393ab-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="393ab-243">Bu gibi `HttpPostAttribute`özniteliklerin rolünü anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="393ab-244">Benzer öznitelikler diğer [HTTP fiilleri](#verb)için tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="393ab-245">[Geleneksel yönlendirmede,](#cr)eylemlerin gösteri formunun bir parçası olduklarında aynı eylem adını kullanması, form iş akışı göndermesi yaygındır.</span><span class="sxs-lookup"><span data-stu-id="393ab-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="393ab-246">Örneğin, [bkz.](xref:tutorials/first-mvc-app/controller-methods-views#get-post)</span><span class="sxs-lookup"><span data-stu-id="393ab-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="393ab-247">Yönlendirme en iyi adayı seçemiyorsa, birden çok eşleşen uç noktayı listeleyen bir <xref:System.Reflection.AmbiguousMatchException> atılmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="393ab-248">Geleneksel rota adları</span><span class="sxs-lookup"><span data-stu-id="393ab-248">Conventional route names</span></span>

<span data-ttu-id="393ab-249">Dizeleri `"blog"` ve `"default"` aşağıdaki örneklerde geleneksel rota adları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="393ab-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="393ab-250">Rota adları rotaya mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="393ab-250">The route names give the route a logical name.</span></span> <span data-ttu-id="393ab-251">Adlandırılmış rota URL üretimi için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="393ab-252">Adlandırılmış bir rotanın kullanılması, yolların sıralanması URL oluşturmayı karmaşık hale getirebileceğinde URL oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="393ab-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="393ab-253">Rota adları benzersiz uygulama genişliğinde olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="393ab-254">Rota adları:</span><span class="sxs-lookup"><span data-stu-id="393ab-254">Route names:</span></span>

* <span data-ttu-id="393ab-255">URL eşleştirme veya isteklerin işlenmesi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="393ab-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="393ab-256">Yalnızca URL üretimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-256">Are used only for URL generation.</span></span>

<span data-ttu-id="393ab-257">Rota adı kavramı [yönlendirmede IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)olarak temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="393ab-258">Terimrota **adı** ve bitiş **noktası adı:**</span><span class="sxs-lookup"><span data-stu-id="393ab-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="393ab-259">Değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-259">Are interchangeable.</span></span>
* <span data-ttu-id="393ab-260">Hangisinin belge ve kodda kullanıldığı açıklanan API'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="393ab-261">REST API'leri için öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="393ab-262">REST API'ler, uygulamanın işlevselliğini işlemlerin [HTTP fiilleri](#verb)tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="393ab-263">Öznitelik yönlendirme, eylemleri doğrudan rota şablonlarına yönlendirmek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="393ab-264">Aşağıdaki `StartUp.Configure` kod REST API için tipiktir ve bir sonraki örnekte kullanılır:</span><span class="sxs-lookup"><span data-stu-id="393ab-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="393ab-265">Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> eşöz `UseEndpoints` yönlendirilmiş denetleyicileri eşlemek için içinde denir.</span><span class="sxs-lookup"><span data-stu-id="393ab-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="393ab-266">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="393ab-266">In the following example:</span></span>

* <span data-ttu-id="393ab-267">Önceki `Configure` yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="393ab-268">`HomeController`varsayılan geleneksel rotanın `{controller=Home}/{action=Index}/{id?}` eşleştiğininbenzer URL'leri kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="393ab-269">Eylem, `HomeController.Index` `/`URL yollarından herhangi biri `/Home` `/Home/Index`için `/Home/Index/3`çalıştırılır, , , veya .</span><span class="sxs-lookup"><span data-stu-id="393ab-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="393ab-270">Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasındaki önemli bir programlama farkvurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="393ab-271">Öznitelik yönlendirme, bir rota belirtmek için daha fazla giriş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="393ab-272">Geleneksel varsayılan rota yolları daha kısa bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="393ab-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="393ab-273">Ancak, öznitelik yönlendirme, her [eylem](#action)için hangi rota şablonlarının geçerli olduğunu kesin olarak denetlemeye izin verir ve gerektirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="393ab-274">Öznitelik yönlendirmesi ile denetleyici adı ve eylem adları eylemin eşleştir olduğu **hiçbir** rol oynamaz.</span><span class="sxs-lookup"><span data-stu-id="393ab-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="393ab-275">Aşağıdaki örnek, önceki örnekle aynı URL'lerle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="393ab-276">Aşağıdaki kod için `action` belirteç `controller`replasmanı kullanır ve:</span><span class="sxs-lookup"><span data-stu-id="393ab-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="393ab-277">Aşağıdaki kod denetleyici `[Route("[controller]/[action]")]` için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="393ab-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="393ab-278">Önceki kodda, `Index` yöntem şablonları rota `/` şablonlarına hazırlanmalıdır. `~/`</span><span class="sxs-lookup"><span data-stu-id="393ab-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="393ab-279">Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="393ab-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="393ab-280">Rota şablonu seçimiyle ilgili bilgi için [Rota şablonu önceliğine](xref:fundamentals/routing#rtp) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="393ab-281">Ayrılmış yönlendirme adları</span><span class="sxs-lookup"><span data-stu-id="393ab-281">Reserved routing names</span></span>

<span data-ttu-id="393ab-282">Denetleyicileri veya Jilet Sayfaları kullanırken aşağıdaki anahtar kelimeler rota parametre adları ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="393ab-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="393ab-283">Öznitelik yönlendirmesi ile bir rota parametresi olarak kullanmak `page` yaygın bir hatadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="393ab-284">Bunu yapmak, URL oluşturma yla tutarsız ve kafa karıştırıcı davranışlara neden olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="393ab-285">Özel parametre adları, URL oluşturma işleminin Bir Jilet Sayfası'na mı yoksa Denetleyiciye mi atıfta bulunduğunu belirlemek için URL oluşturma tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="393ab-286">HTTP fiil şablonları</span><span class="sxs-lookup"><span data-stu-id="393ab-286">HTTP verb templates</span></span>

<span data-ttu-id="393ab-287">ASP.NET Core aşağıdaki HTTP fiil şablonları vardır:</span><span class="sxs-lookup"><span data-stu-id="393ab-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="393ab-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="393ab-289">[[Posta]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="393ab-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="393ab-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="393ab-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="393ab-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="393ab-294">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="393ab-294">Route templates</span></span>

<span data-ttu-id="393ab-295">ASP.NET Core aşağıdaki rota şablonlarına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="393ab-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="393ab-296">Tüm [HTTP fiil şablonları](#verb) rota şablonlarıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="393ab-297">[[Rota]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="393ab-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="393ab-298">Http fiil öznitelikleri ile öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="393ab-299">Aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="393ab-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="393ab-300">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="393ab-300">In the preceding code:</span></span>

* <span data-ttu-id="393ab-301">Her eylem, `[HttpGet]` yalnızca HTTP GET istekleriyle eşleşen öznitelik içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="393ab-302">`GetProduct` Eylem şablonu `"{id}"` içerir, `id` bu nedenle `"api/[controller]"` denetleyicideki şablona eklenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="393ab-303">Yöntem şablonu. `"api/[controller]/"{id}""`</span><span class="sxs-lookup"><span data-stu-id="393ab-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="393ab-304">Bu nedenle bu eylem sadece form `/api/test2/xyz``/api/test2/123`için`/api/test2/{any string}`GET isteklerieş , , , vb.</span><span class="sxs-lookup"><span data-stu-id="393ab-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="393ab-305">`GetIntProduct` Eylem şablonu `"int/{id:int}")` içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="393ab-306">Şablonun `:int` `id` bölümü, rota değerlerini tamsayıya dönüştürülebilecek dizeleri kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="393ab-307">Bir GET `/api/test2/int/abc`isteği :</span><span class="sxs-lookup"><span data-stu-id="393ab-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="393ab-308">Bu hareketle uyuşmuyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="393ab-309">[404 Bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hata verir.</span><span class="sxs-lookup"><span data-stu-id="393ab-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="393ab-310">Eylem `GetInt2Product` şablonda içerir, `{id}` ancak bir karşıcıya dönüştürülebilecek değerlerle sınırlandırmaz. `id`</span><span class="sxs-lookup"><span data-stu-id="393ab-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="393ab-311">Bir GET `/api/test2/int2/abc`isteği :</span><span class="sxs-lookup"><span data-stu-id="393ab-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="393ab-312">Bu rotayla eşleşiyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-312">Matches this route.</span></span>
  * <span data-ttu-id="393ab-313">Model bağlama bir `abc` karşıcıya dönüştürmede başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="393ab-314">Yöntemin `id` parametresi, tümsecidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="393ab-315">Model bağlama bir tamsayıya dönüştürülemediğinden`abc` [400 Kötü İstek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür.</span><span class="sxs-lookup"><span data-stu-id="393ab-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="393ab-316">Öznitelik yönlendirme, , <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> ve <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. gibi öznitelikleri kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="393ab-317">[TÜM HTTP fiil](#verb) öznitelikleri bir rota şablonu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="393ab-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="393ab-318">Aşağıdaki örnek, aynı rota şablonuyla eşleşen iki eylemi gösterir:</span><span class="sxs-lookup"><span data-stu-id="393ab-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="393ab-319">URL yolunu `/products3`kullanma:</span><span class="sxs-lookup"><span data-stu-id="393ab-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="393ab-320">`MyProductsController.ListProducts` EYLEM, HTTP [fiili](#verb) . `GET`</span><span class="sxs-lookup"><span data-stu-id="393ab-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="393ab-321">`MyProductsController.CreateProduct` EYLEM, HTTP [fiili](#verb) . `POST`</span><span class="sxs-lookup"><span data-stu-id="393ab-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="393ab-322">BIR REST API'si inşa ederken, eylem tüm `[Route(...)]` HTTP yöntemlerini kabul ettiğinden, bir eylem yönteminde kullanmanız gereken nadirdir.</span><span class="sxs-lookup"><span data-stu-id="393ab-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="393ab-323">API'nizin neyi desteklediği hakkında kesin olmak için daha spesifik [HTTP fiil özniteliğini](#verb) kullanmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="393ab-324">REST API'lerinin istemcilerinin belirli mantıksal işlemlere hangi yolların ve HTTP fiillerinin eşlenebildiğini bilmeleri beklenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="393ab-325">REST API'leri, uygulamanın işlevselliğini işlemlerin HTTP fiilleri tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="393ab-326">Bu, aynı mantıksal kaynaktaki GET ve POST gibi birçok operasyonun aynı URL'yi kullandığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="393ab-327">Öznitelik yönlendirme, bir API'nin genel uç nokta düzenini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="393ab-328">Bir öznitelik rotası belirli bir eylem için geçerli olduğundan, rota şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="393ab-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="393ab-329">Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:</span><span class="sxs-lookup"><span data-stu-id="393ab-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="393ab-330">Eylem: `Products2ApiController.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="393ab-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="393ab-331">GIBI URL yolu ile çalıştırılır mı`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="393ab-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="393ab-332">URL yolu `/products2`ile çalıştırılmıyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="393ab-333">[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlamak için bir eyleme izin verir.</span><span class="sxs-lookup"><span data-stu-id="393ab-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="393ab-334">Daha fazla bilgi için [bkz.](xref:web-api/index#consumes)</span><span class="sxs-lookup"><span data-stu-id="393ab-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="393ab-335">Rota şablonlarının ve ilgili seçeneklerin tam açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="393ab-336">Daha fazla `[ApiController]`bilgi için [ApiController özniteliğine](xref:web-api/index##apicontroller-attribute)bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="393ab-337">Yönlendirme adı</span><span class="sxs-lookup"><span data-stu-id="393ab-337">Route name</span></span>

<span data-ttu-id="393ab-338">Aşağıdaki kod bir rota adı `Products_List`tanımlar:</span><span class="sxs-lookup"><span data-stu-id="393ab-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="393ab-339">Rota adları, belirli bir rotayı temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="393ab-340">Rota adları:</span><span class="sxs-lookup"><span data-stu-id="393ab-340">Route names:</span></span>

* <span data-ttu-id="393ab-341">Yönlendirmenin URL eşleştirme davranışı üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="393ab-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="393ab-342">Yalnızca URL üretimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-342">Are only used for URL generation.</span></span>

<span data-ttu-id="393ab-343">Rota adları uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="393ab-344">Önceki kodu, parametreyi `id` isteğe bağlı olarak tanımlayan geleneksel`{id?}`varsayılan rotayla karşılayın ( ).</span><span class="sxs-lookup"><span data-stu-id="393ab-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="393ab-345">API'leri tam olarak belirtme olanağının, `/products` farklı `/products/5` eylemlere izin verme ve gönderme gibi avantajları vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="393ab-346">Öznitelik yollarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="393ab-346">Combining attribute routes</span></span>

<span data-ttu-id="393ab-347">Öznitelik yönlendirmeyi daha az yinelenen hale getirmek için denetleyicideki rota öznitelikleri, tek tek eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="393ab-348">Denetleyicide tanımlanan tüm rota şablonları, eylemlerdeki şablonları yönlendirmeye hazırlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="393ab-349">Denetleyiciye bir rota özniteliği yerleştirmek, denetleyicideki **tüm** eylemleri öznitelik yönlendirmesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="393ab-350">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="393ab-350">In the preceding example:</span></span>

* <span data-ttu-id="393ab-351">URL yolu `/products` eşleşebilir`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="393ab-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="393ab-352">URL yolu `/products/5` eşleşebilir. `ProductsApi.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="393ab-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="393ab-353">Bu eylemlerin her ikisi `GET` de yalnızca HTTP `[HttpGet]` ile eşleşir, çünkü öznitelik ile işaretlenirler.</span><span class="sxs-lookup"><span data-stu-id="393ab-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="393ab-354">Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="393ab-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="393ab-355">Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolu kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="393ab-356">Aşağıdaki tabloda `[Route]` önceki koddaki öznitelikler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="393ab-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="393ab-357">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="393ab-357">Attribute</span></span>               | <span data-ttu-id="393ab-358">Ile birleştirir`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="393ab-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="393ab-359">Rota şablonu tanımlar</span><span class="sxs-lookup"><span data-stu-id="393ab-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="393ab-360">Evet</span><span class="sxs-lookup"><span data-stu-id="393ab-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="393ab-361">Evet</span><span class="sxs-lookup"><span data-stu-id="393ab-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="393ab-362">**Hayır**</span><span class="sxs-lookup"><span data-stu-id="393ab-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="393ab-363">Evet</span><span class="sxs-lookup"><span data-stu-id="393ab-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="393ab-364">Öznitelik rota sırası</span><span class="sxs-lookup"><span data-stu-id="393ab-364">Attribute route order</span></span>

<span data-ttu-id="393ab-365">Yönlendirme bir ağaç oluşturur ve tüm uç noktaları eş zamanlı olarak eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="393ab-366">Rota girişleri ideal bir sıralamaya yerleştirilmiş gibi olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="393ab-367">En özel rotalar, daha genel rotalardan önce yürütme şansına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="393ab-368">Örneğin, gibi `blog/search/{topic}` bir öznitelik yolu gibi `blog/{*article}`bir öznitelik yolu daha özeldir.</span><span class="sxs-lookup"><span data-stu-id="393ab-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="393ab-369">Rota, `blog/search/{topic}` varsayılan olarak daha yüksek önceliğe sahiptir, çünkü daha spesifiktir.</span><span class="sxs-lookup"><span data-stu-id="393ab-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="393ab-370">[Geleneksel yönlendirmeyi](#cr)kullanarak, geliştirici yolları istenilen sıraya yerleştirmekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="393ab-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="393ab-371">Öznitelik yolları <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> özelliği kullanarak bir sipariş yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="393ab-372">Sağlanan tüm çerçeve rota `Order` [öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="393ab-373">Rotalar, artan bir `Order` özellik türüne göre işlenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="393ab-374">Varsayılan sıra. `0`</span><span class="sxs-lookup"><span data-stu-id="393ab-374">The default order is `0`.</span></span> <span data-ttu-id="393ab-375">Sipariş belirlemeyen `Order = -1` rotalardan önce çalıştırmaları kullanarak rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="393ab-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="393ab-376">Varsayılan rota `Order = 1` siparişinden sonra çalışır kullanarak bir rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="393ab-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="393ab-377">Bağlı olarak `Order` **kaçının.**</span><span class="sxs-lookup"><span data-stu-id="393ab-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="393ab-378">Bir uygulamanın URL alanı doğru yönlendirmek için açık sipariş değerleri gerektiriyorsa, istemciler için de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="393ab-379">Genel olarak, öznitelik yönlendirme, URL eşlemeile doğru rotayı seçer.</span><span class="sxs-lookup"><span data-stu-id="393ab-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="393ab-380">URL oluşturma için kullanılan varsayılan sipariş çalışmıyorsa, geçersiz kılma olarak bir rota adı `Order` kullanmak genellikle özelliği uygulamaktan daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="393ab-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="393ab-381">Her ikisi de rota eşleşen `/home`tanımlamak aşağıdaki iki denetleyicileri düşünün:</span><span class="sxs-lookup"><span data-stu-id="393ab-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="393ab-382">Önceki `/home` kodla birlikte istemek aşağıdakilere benzer bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="393ab-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="393ab-383">Rota `Order` özniteliklerinden birine ekleme belirsizliği giderir:</span><span class="sxs-lookup"><span data-stu-id="393ab-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="393ab-384">Önceki kodla bitiş `/home` noktasını `HomeController.Index` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="393ab-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="393ab-385">Almak için `MyDemoController.MyIndex`, `/home/MyIndex`istek .</span><span class="sxs-lookup"><span data-stu-id="393ab-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="393ab-386">**Not**:</span><span class="sxs-lookup"><span data-stu-id="393ab-386">**Note**:</span></span>

* <span data-ttu-id="393ab-387">Önceki kod bir örnek veya kötü yönlendirme tasarımıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="393ab-388">`Order` Mülkü göstermek için kullanılmış.</span><span class="sxs-lookup"><span data-stu-id="393ab-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="393ab-389">Özellik `Order` yalnızca belirsizliği giderir, bu şablon eşleşemez.</span><span class="sxs-lookup"><span data-stu-id="393ab-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="393ab-390">Şablonu `[Route("Home")]` kaldırmak daha iyi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="393ab-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="393ab-391">[Bkz. Razor Pages rota ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) Razor Pages ile rota sırası hakkında bilgi için rota sırası.</span><span class="sxs-lookup"><span data-stu-id="393ab-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="393ab-392">Bazı durumlarda, bir HTTP 500 hatası belirsiz yollarla döndürülür.</span><span class="sxs-lookup"><span data-stu-id="393ab-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="393ab-393">Hangi uç noktaların neden `AmbiguousMatchException`olduğunu görmek için [günlüğe kaydetmeyi](xref:fundamentals/logging/index) kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="393ab-394">Rota şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]</span><span class="sxs-lookup"><span data-stu-id="393ab-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="393ab-395">Kolaylık sağlamak için, öznitelik rotaları aşağıdakilerden birine bir belirteç ekleyerek ayrılmış rota parametreleri için belirteç değişimini destekler:</span><span class="sxs-lookup"><span data-stu-id="393ab-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="393ab-396">Kare ayraçlar:`[]`</span><span class="sxs-lookup"><span data-stu-id="393ab-396">Square braces: `[]`</span></span>
* <span data-ttu-id="393ab-397">Kıvırcık ayraçlar:`{}`</span><span class="sxs-lookup"><span data-stu-id="393ab-397">Curly braces: `{}`</span></span>

<span data-ttu-id="393ab-398">Yol `[action]` `[area]`tanımlandığı eylemden `[controller]` eylem adı, alan adı ve denetleyici adının değerleri ile değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="393ab-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="393ab-399">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="393ab-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="393ab-400">Eşleşen`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="393ab-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="393ab-401">Eşleşen`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="393ab-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="393ab-402">Belirteç değiştirme, öznitelik yollarını oluşturmanın son adımı olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="393ab-403">Önceki örnek aşağıdaki kodla aynı şekilde davranışta dır:</span><span class="sxs-lookup"><span data-stu-id="393ab-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="393ab-404">Öznitelik yolları da devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="393ab-405">Bu belirteç değiştirme ile birlikte güçlüdür.</span><span class="sxs-lookup"><span data-stu-id="393ab-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="393ab-406">Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="393ab-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`her eylem için benzersiz bir rota adı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="393ab-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="393ab-408">Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="393ab-409">her eylem için benzersiz bir rota adı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="393ab-410">Gerçek belirteç değiştirme delimiter `[` `]`maç veya , karakter (veya)`[[` `]]`tekrarlayarak kaçmak.</span><span class="sxs-lookup"><span data-stu-id="393ab-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="393ab-411">Belirteç değiştirmesini özelleştirmek için parametre transformatörü kullanma</span><span class="sxs-lookup"><span data-stu-id="393ab-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="393ab-412">Belirteç değiştirme parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="393ab-413">Bir parametre transformatörü parametrelerin değerini uygular <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="393ab-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="393ab-414">Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini aşağıdakilere değiştirir:</span><span class="sxs-lookup"><span data-stu-id="393ab-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="393ab-415">Bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> uygulama modeli kuralı:</span><span class="sxs-lookup"><span data-stu-id="393ab-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="393ab-416">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="393ab-417">Değiştirildikleri gibi öznitelik rotası belirteç değerlerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="393ab-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="393ab-418">Önceki `ListAll` yöntem eşleşir. `/subscription-management/list-all`</span><span class="sxs-lookup"><span data-stu-id="393ab-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="393ab-419">Bir `RouteTokenTransformerConvention` seçenek olarak `ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="393ab-420">Sümüklüböcek tanımı için [Slug'daki MDN web dokümanlarına](https://developer.mozilla.org/docs/Glossary/Slug) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="393ab-421">Birden çok öznitelik yolu</span><span class="sxs-lookup"><span data-stu-id="393ab-421">Multiple attribute routes</span></span>

<span data-ttu-id="393ab-422">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yol tanımlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="393ab-423">Bunun en yaygın kullanımı, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel rotanın davranışını taklit etmektir:</span><span class="sxs-lookup"><span data-stu-id="393ab-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="393ab-424">Denetleyiciye birden çok yol öznitelikleri koymak, her birinin eylem yöntemlerindeki her bir rota öznitelikleriyle birleştiği anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="393ab-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="393ab-425">Tüm [HTTP fiil](#verb) yolu `IActionConstraint`kısıtlamaları uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="393ab-426">Uygulayan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> birden çok rota öznitelikleri bir eyleme yerleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="393ab-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="393ab-427">Her eylem kısıtlaması denetleyiciye uygulanan rota şablonuyla birleştirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="393ab-428">Eylemler üzerinde birden fazla rota kullanmak yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="393ab-429">Varolan istemcileri desteklemek için **yalnızca** gerektiğinde eylemlerde birden çok yol kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="393ab-430">Öznitelik rotası isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtme</span><span class="sxs-lookup"><span data-stu-id="393ab-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="393ab-431">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır sıralı sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="393ab-432">Önceki kodda, `[HttpPost("product/{id:int}")]` bir rota kısıtlaması uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="393ab-433">Eylem `ProductsController.ShowProduct` yalnızca `/product/3`URL yollarla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="393ab-434">Rota şablonu `{id:int}` bölümü, bu kesimi yalnızca tamsayılarla kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="393ab-435">Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="393ab-436">IRouteTemplateProvider kullanarak özel rota öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="393ab-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="393ab-437">Tüm [rota öznitelikleri](#rt) uygular. <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider></span><span class="sxs-lookup"><span data-stu-id="393ab-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="393ab-438">ASP.NET Core çalışma zamanı:</span><span class="sxs-lookup"><span data-stu-id="393ab-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="393ab-439">Uygulama başladığında denetleyici sınıfları ve eylem yöntemleriyle ilgili öznitelikleri arar.</span><span class="sxs-lookup"><span data-stu-id="393ab-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="393ab-440">İlk rota kümesini oluşturmak için uygulanan `IRouteTemplateProvider` öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="393ab-441">Özel `IRouteTemplateProvider` rota özniteliklerini tanımlamak için uygulayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="393ab-442">Her `IRouteTemplateProvider` biri özel bir rota şablonu, sipariş ve ad ile tek bir rota tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="393ab-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="393ab-443">Önceki `Get` yöntem döndürür. `Order = 2, Template = api/MyTestApi`</span><span class="sxs-lookup"><span data-stu-id="393ab-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="393ab-444">Öznitelik rotalarını özelleştirmek için uygulama modelini kullanma</span><span class="sxs-lookup"><span data-stu-id="393ab-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="393ab-445">Uygulama modeli:</span><span class="sxs-lookup"><span data-stu-id="393ab-445">The application model:</span></span>

* <span data-ttu-id="393ab-446">Başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="393ab-447">ASP.NET Core tarafından bir uygulamadaki eylemleri yönlendirmek ve yürütmek için kullanılan tüm meta verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="393ab-448">Uygulama modeli, rota özniteliklerinden toplanan tüm verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="393ab-449">Rota özniteliklerinden gelen veriler `IRouteTemplateProvider` uygulama tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="393ab-450">Kural -ları:</span><span class="sxs-lookup"><span data-stu-id="393ab-450">Conventions:</span></span>

* <span data-ttu-id="393ab-451">Yönlendirmenin nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde gerçekleştirileceğini özelleştirmek için uygulama modelini değiştirmek için yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="393ab-452">Uygulama başlangıç noktasında okunur.</span><span class="sxs-lookup"><span data-stu-id="393ab-452">Are read at app startup.</span></span>

<span data-ttu-id="393ab-453">Bu bölümde, uygulama modelini kullanarak yönlendirme özelleştirme temel bir örnek gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="393ab-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="393ab-454">Aşağıdaki kod, yolların kabaca projenin klasör yapısıyla hizaya dizilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="393ab-455">Aşağıdaki kod, öznitelik yönlendirilmiş denetleyicilere kuralın `namespace` uygulanmasını engeller:</span><span class="sxs-lookup"><span data-stu-id="393ab-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="393ab-456">Örneğin, aşağıdaki denetleyici aşağıdakileri `NamespaceRoutingConvention`kullanmaz:</span><span class="sxs-lookup"><span data-stu-id="393ab-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="393ab-457">Yöntem: `NamespaceRoutingConvention.Apply`</span><span class="sxs-lookup"><span data-stu-id="393ab-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="393ab-458">Denetleyici öznitelik yönlendirilmişse hiçbir şey yapmaz.</span><span class="sxs-lookup"><span data-stu-id="393ab-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="393ab-459">Denetleyicişablonu `namespace`, tabanı `namespace` kaldırılmış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="393ab-460">`Startup.ConfigureServices`Uygulanabilir: `NamespaceRoutingConvention`</span><span class="sxs-lookup"><span data-stu-id="393ab-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="393ab-461">Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="393ab-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="393ab-462">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="393ab-462">In the preceding code:</span></span>

* <span data-ttu-id="393ab-463">`My.Application`Üs. `namespace`</span><span class="sxs-lookup"><span data-stu-id="393ab-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="393ab-464">Önceki denetleyicinin tam `My.Application.Admin.Controllers.UsersController`adı.</span><span class="sxs-lookup"><span data-stu-id="393ab-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="393ab-465">Denetleyicişablonu `NamespaceRoutingConvention` `Admin/Controllers/Users/[action]/{id?`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="393ab-466">Denetleyiciye `NamespaceRoutingConvention` öznitelik olarak da uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="393ab-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="393ab-467">Karışık yönlendirme: Atfetme yönlendirme vs konvansiyonel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="393ab-468">ASP.NET Core uygulamaları geleneksel yönlendirme ve öznitelik yönlendirme kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="393ab-469">Tarayıcılar için HTML sayfaları sunan denetleyiciler için geleneksel rotalar kullanmak ve REST API'leri sunan denetleyiciler için yönlendirme özelliği kullanmak tipiktir.</span><span class="sxs-lookup"><span data-stu-id="393ab-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="393ab-470">Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="393ab-471">Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="393ab-472">Öznitelik yollarını tanımlayan eylemlere geleneksel yollardan ve bunun tersi üzerinden ulaşılamıyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="393ab-473">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki **tüm** eylemleri yönlendirilen yapar.</span><span class="sxs-lookup"><span data-stu-id="393ab-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="393ab-474">Öznitelik yönlendirme ve konvansiyonel yönlendirme aynı yönlendirme motorkullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="393ab-475">URL Oluşturma ve ortam değerleri</span><span class="sxs-lookup"><span data-stu-id="393ab-475">URL Generation and ambient values</span></span>

<span data-ttu-id="393ab-476">Uygulamalar, eylemlere URL bağlantıları oluşturmak için yönlendirme URL oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="393ab-477">URL'ler üretmek, kodlama URL'lerini ortadan kaldırarak kodu daha sağlam ve korunabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="393ab-478">Bu bölümde, MVC tarafından sağlanan URL oluşturma özellikleri üzerinde duruluyor ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsamaktadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="393ab-479">URL oluşturmanın ayrıntılı bir açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="393ab-480">Arabirim, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> MVC ile URL üretimi için yönlendirme arasındaki altyapının temel öğesidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="393ab-481">Bir örneği `IUrlHelper` denetleyiciler, `Url` görünümler ve görünüm bileşenleri özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="393ab-482">Aşağıdaki örnekte, `IUrlHelper` arabirim `Controller.Url` başka bir eylem için bir URL oluşturmak için özellik üzerinden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="393ab-483">Uygulama varsayılan geleneksel rotayı kullanıyorsa, `url` değişkenin değeri URL `/UrlGeneration/Destination`yol dizesidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="393ab-484">Bu URL yolu, şu yolu birleştirerek yönlendirme yle oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="393ab-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="393ab-485">**Ortam değerleri**olarak adlandırılan geçerli istekten rota değerleri.</span><span class="sxs-lookup"><span data-stu-id="393ab-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="393ab-486">Bu değerlere `Url.Action` geçirilen ve bu değerlerin yerine geçen değerler:</span><span class="sxs-lookup"><span data-stu-id="393ab-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="393ab-487">Rota şablonundaki her rota parametresinin değeri, adları değerler ve ortam değerleriyle eşleştirerek ikame edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="393ab-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="393ab-488">Değeri olmayan bir rota parametresi şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="393ab-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="393ab-489">Varsa varsayılan bir değer kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="393ab-490">İsteğe bağlıysa atlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-490">Be skipped if it's optional.</span></span> <span data-ttu-id="393ab-491">Örneğin, rota `id` şablonundan. `{controller}/{action}/{id?}`</span><span class="sxs-lookup"><span data-stu-id="393ab-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="393ab-492">Gerekli rota parametresi karşılık gelen bir değere sahip değilse URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="393ab-493">URL oluşturma bir rota için başarısız olursa, sonraki rota tüm yollar denenene veya eşleşme bulunana kadar denendi.</span><span class="sxs-lookup"><span data-stu-id="393ab-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="393ab-494">Önceki örnek, `Url.Action` geleneksel [yönlendirmeyi](#cr)varsayar.</span><span class="sxs-lookup"><span data-stu-id="393ab-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="393ab-495">URL oluşturma, kavramlar farklı olsa da [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="393ab-496">Konvansiyonel yönlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="393ab-496">With conventional routing:</span></span>

* <span data-ttu-id="393ab-497">Rota değerleri şablonu genişletmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="393ab-498">Bu şablonda `controller` `action` genellikle rota değerleri görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="393ab-499">Yönlendirme yle eşleşen URL'ler bir sözleşmeye uyduğu için bu işe yarar.</span><span class="sxs-lookup"><span data-stu-id="393ab-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="393ab-500">Aşağıdaki örneköznite yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="393ab-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="393ab-501">Önceki `Source` koddaki eylem oluşturur. `custom/url/to/destination`</span><span class="sxs-lookup"><span data-stu-id="393ab-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="393ab-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>alternatif olarak Core 3.0ASP.NET eklendi. `IUrlHelper`</span><span class="sxs-lookup"><span data-stu-id="393ab-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="393ab-503">`LinkGenerator`benzer ancak daha esnek işlevsellik sunar.</span><span class="sxs-lookup"><span data-stu-id="393ab-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="393ab-504">Her yöntem `IUrlHelper` üzerinde `LinkGenerator` de ilgili bir yöntem ailesi vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="393ab-505">Eylem adına göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-505">Generating URLs by action name</span></span>

<span data-ttu-id="393ab-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), ve ilgili tüm overloads tüm bir denetleyici adı ve eylem adı belirterek hedef bitiş noktası oluşturmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="393ab-507">Kullanırken, `Url.Action`geçerli rota `controller` değerleri `action` için ve çalışma zamanı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="393ab-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="393ab-508">Değeri `controller` ve `action` hem ortam değerleri ve [değerlerinin](#ambient) bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="393ab-509">Yöntem `Url.Action` her zaman geçerli `action` değerleri `controller` kullanır ve geçerli eyleme giden bir URL yolu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="393ab-510">Yönlendirme, URL oluştururken sağlanmadı bilgileri doldurmak için ortam değerlerindeki değerleri kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="393ab-511">Ortam değerleri `{ a = Alice, b = Bob, c = Carol, d = David }` `{a}/{b}/{c}/{d}` gibi bir rota düşünün:</span><span class="sxs-lookup"><span data-stu-id="393ab-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="393ab-512">Yönlendirme, ek değerler olmadan bir URL oluşturmak için yeterli bilgiye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="393ab-513">Yönlendirme, tüm rota parametrelerinin bir değeri olduğundan yeterli bilgiye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="393ab-514">Katma değer `{ d = Donovan }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="393ab-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="393ab-515">Değer `{ d = David }` yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="393ab-516">Oluşturulan URL `Alice/Bob/Carol/Donovan`yolu.</span><span class="sxs-lookup"><span data-stu-id="393ab-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="393ab-517">**Uyarı**: URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="393ab-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="393ab-518">Önceki örnekte, değer `{ c = Cheryl }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="393ab-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="393ab-519">Her iki `{ c = Carol, d = David }` değer de yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="393ab-520">Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="393ab-521">Bir URL `c` oluşturmak `d` için istenen değerler belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="393ab-522">Varsayılan rota `{controller}/{action}/{id?}`ile bu sorunu vurmak bekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="393ab-523">Her zaman a `Url.Action` `controller` ve `action` değeri açıkça belirttiğinden, bu sorun pratikte nadirdir.</span><span class="sxs-lookup"><span data-stu-id="393ab-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="393ab-524">[Url.Action'ın](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) birkaç aşırı yükü, rota parametreleri için `controller` değerler `action`sağlamak için bir rota değerleri nesnesi alır ve .</span><span class="sxs-lookup"><span data-stu-id="393ab-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="393ab-525">Rota değerleri nesnesi sık `id`sık .</span><span class="sxs-lookup"><span data-stu-id="393ab-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="393ab-526">Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="393ab-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="393ab-527">Rota değerleri nesnesi:</span><span class="sxs-lookup"><span data-stu-id="393ab-527">The route values object:</span></span>

* <span data-ttu-id="393ab-528">Kural olarak genellikle anonim türde bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="393ab-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="393ab-529">Bir `IDictionary<>` veya [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).</span><span class="sxs-lookup"><span data-stu-id="393ab-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="393ab-530">Rota parametreleri ile eşleşmeyen ek rota değerleri sorgu dizesinde konur.</span><span class="sxs-lookup"><span data-stu-id="393ab-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="393ab-531">Önceki kod `/Products/Buy/17?color=red`oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="393ab-532">Aşağıdaki kod mutlak bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="393ab-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="393ab-533">Mutlak bir URL oluşturmak için aşağıdakilerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="393ab-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="393ab-534">Bir aşırı yük kabul `protocol`eder.</span><span class="sxs-lookup"><span data-stu-id="393ab-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="393ab-535">Örneğin, önceki kod.</span><span class="sxs-lookup"><span data-stu-id="393ab-535">For example, the preceding code.</span></span>
* <span data-ttu-id="393ab-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), varsayılan olarak mutlak URIs oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="393ab-537">Rotaya göre URL'ler oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-537">Generate URLs by route</span></span>

<span data-ttu-id="393ab-538">Önceki kod denetleyici ve eylem adı geçerek bir URL oluşturma gösterdi.</span><span class="sxs-lookup"><span data-stu-id="393ab-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="393ab-539">`IUrlHelper`ayrıca [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) yöntemleri ailesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="393ab-540">Bu yöntemler [Url.Action'a](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)benzer, ancak geçerli değerleri `action` ve `controller` rota değerlerini kopyalamaz.</span><span class="sxs-lookup"><span data-stu-id="393ab-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="393ab-541">En yaygın `Url.RouteUrl`kullanımı:</span><span class="sxs-lookup"><span data-stu-id="393ab-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="393ab-542">URL'yi oluşturmak için bir rota adı belirtir.</span><span class="sxs-lookup"><span data-stu-id="393ab-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="393ab-543">Genellikle bir denetleyici veya eylem adı belirtmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="393ab-544">Aşağıdaki Razor dosyası için bir HTML `Destination_Route`bağlantısı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="393ab-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="393ab-545">HTML ve Razor'da URL'ler oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="393ab-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> [html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini `<form>` `<a>` sağlar ve sırasıyla öğeleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="393ab-547">Bu yöntemler url oluşturmak için [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="393ab-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="393ab-548">Bunun `Url.RouteUrl` için `HtmlHelper` refakatçive `Html.BeginRouteForm` `Html.RouteLink` benzer işlevsellik vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="393ab-549">`form` TagHelpers, TagHelper ve `<a>` TagHelper aracılığıyla URL'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="393ab-550">Bunların her `IUrlHelper` ikisi de bunların uygulanması için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="393ab-551">Daha fazla bilgi için [Formlarda Yardımcıları Etiketle'](xref:mvc/views/working-with-forms) ye bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="393ab-552">İç görünümler, `IUrlHelper` yukarıda `Url` yer almayan herhangi bir özel URL nesli için özellik üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="393ab-553">Eylem Sonuçlarında URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-553">URL generation in Action Results</span></span>

<span data-ttu-id="393ab-554">Önceki örnekler bir `IUrlHelper` denetleyicide kullanılarak gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="393ab-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="393ab-555">Denetleyicide en yaygın kullanım, eylem sonucunun bir parçası olarak bir URL oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="393ab-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="393ab-556">Ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuran eylem sonuçları için kolaylık yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="393ab-557">Tipik bir kullanım kullanıcı girişi kabul ettikten sonra yeniden yönlendirmek için:</span><span class="sxs-lookup"><span data-stu-id="393ab-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="393ab-558">Eylem gibi fabrika yöntemleri <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> sonuçları ve üzerinde `IUrlHelper`yöntemlere benzer bir desen izleyin.</span><span class="sxs-lookup"><span data-stu-id="393ab-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="393ab-559">Özel konvansiyonel rotalar için özel durum</span><span class="sxs-lookup"><span data-stu-id="393ab-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="393ab-560">[Konvansiyonel yönlendirme,](#cr) özel bir [konvansiyonel rota](#dcr)adı verilen özel bir rota tanımı türü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="393ab-561">Aşağıdaki örnekte, adlandırılmış `blog` rota özel bir geleneksel rotadır:</span><span class="sxs-lookup"><span data-stu-id="393ab-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="393ab-562">Önceki rota tanımlarını kullanarak, `Url.Action("Index", "Home")` `/` `default` yolu kullanarak URL yolunu oluşturur, ancak neden?</span><span class="sxs-lookup"><span data-stu-id="393ab-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="393ab-563">Rota değerlerinin `{ controller = Home, action = Index }` bir URL oluşturmak `blog`için yeterli olacağını tahmin edebilirsiniz ve sonuç . `/blog?action=Index&controller=Home`</span><span class="sxs-lookup"><span data-stu-id="393ab-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="393ab-564">[Özel geleneksel rotalar,](#dcr) rotanın URL oluşturma yla çok [açgözlü](xref:fundamentals/routing#greedy) olmasını engelleyen karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="393ab-565">Bu durumda varsayılan değerler `{ controller = Blog, action = Article }`, `controller` ve `action` ne ne de bir rota parametresi olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="393ab-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="393ab-566">Yönlendirme URL oluşturma gerçekleştirirken, sağlanan değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="393ab-567">Değerler `blog` `{ controller = Home, action = Index }` eşleşmediği `{ controller = Blog, action = Article }`için URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="393ab-568">Yönlendirme sonra denemek `default`için geri düşer , hangi başarılı.</span><span class="sxs-lookup"><span data-stu-id="393ab-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="393ab-569">Alanlar</span><span class="sxs-lookup"><span data-stu-id="393ab-569">Areas</span></span>

<span data-ttu-id="393ab-570">[Alanlar,](xref:mvc/controllers/areas) ilgili işlevleri ayrı olarak bir gruba düzenlemek için kullanılan bir MVC özelliğidir:</span><span class="sxs-lookup"><span data-stu-id="393ab-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="393ab-571">Denetleyici eylemler için ad alanını yönlendirme.</span><span class="sxs-lookup"><span data-stu-id="393ab-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="393ab-572">Görünümler için klasör yapısı.</span><span class="sxs-lookup"><span data-stu-id="393ab-572">Folder structure for views.</span></span>

<span data-ttu-id="393ab-573">Alanları kullanmak, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyiciye sahip olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="393ab-574">Alanları kullanmak, `area` başka bir rota parametresi ekleyerek yönlendirme amacıyla bir `controller` `action`hiyerarşi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="393ab-575">Bu bölümde yönlendirmenin alanlarla nasıl etkileştiği açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="393ab-576">Görünümlerle alanların nasıl kullanıldığıyla ilgili ayrıntılar için [Alanlar'a](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="393ab-577">Aşağıdaki örnekte, Varsayılan geleneksel rotayı ve `area` adlandırılmış `Blog` `area` bir rotayı kullanacak Şekilde MVC yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="393ab-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="393ab-578">Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> oluşturmak için `"blog_route"`çağrılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="393ab-579">İkinci parametre, `"Blog"`alan adıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="393ab-580">Bir URL yolu `/Manage/Users/AddUser`gibi `"blog_route"` eşleştirirken, rota `{ area = Blog, controller = Users, action = AddUser }`rota değerlerini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="393ab-581">Rota `area` değeri için varsayılan değer `area`tarafından üretilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="393ab-582">Oluşturulan `MapAreaControllerRoute` rota aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="393ab-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="393ab-583">`MapAreaControllerRoute`bu durumda, `Blog`sağlanan alan adını kullanmak `area` için hem varsayılan değeri hem de kısıtlamayı kullanarak bir rota oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="393ab-584">Varsayılan değer, rotanın her `{ area = Blog, ... }`zaman üretir, kısıtlama `{ area = Blog, ... }` URL oluşturma için değer gerektirir sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="393ab-585">Konvansiyonel yönlendirme isteðe baðlð±r.</span><span class="sxs-lookup"><span data-stu-id="393ab-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="393ab-586">Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik oldukları için daha erken yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="393ab-587">Önceki örneği kullanarak, rota `{ area = Blog, controller = Users, action = AddUser }` değerleri aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="393ab-588">[[Alan]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir denetleyiciyi bir alanın parçası olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="393ab-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="393ab-589">Bu denetleyici `Blog` bölgede.</span><span class="sxs-lookup"><span data-stu-id="393ab-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="393ab-590">Özniteliği olmayan `[Area]` denetleyiciler herhangi bir alanın üyesi değildir ve `area` rota değeri yönlendirme ile sağlandığında **eşleşmez.**</span><span class="sxs-lookup"><span data-stu-id="393ab-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="393ab-591">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota `{ area = Blog, controller = Users, action = AddUser }`değerleriyle eşleşebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="393ab-592">Her denetleyicinin ad alanı tamolmak için burada gösterilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="393ab-593">Önceki denetleyiciler aynı ad alanını kullanırsa, derleyici hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="393ab-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="393ab-594">Sınıf ad alanlarının MVC'nin yönlendirmesi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="393ab-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="393ab-595">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adları `area` rota değeri tarafından sağlandığında eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="393ab-596">Üçüncü denetleyici herhangi bir alanın üyesi değildir ve yalnızca yönlendirme `area` ile değer sağlanmadığı zaman eşleşebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="393ab-597">Hiçbir *değer*eşleştirme açısından, `area` değer yokluğu için `area` değer null veya boş dize sanki aynıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="393ab-598">Bir alan içinde bir eylem yürütürken, rota değeri `area` URL oluşturma için kullanılacak yönlendirme için ortam [değeri](#ambient) olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="393ab-599">Bu, varsayılan olarak alanların URL oluşturma için aşağıdaki örnekte gösterildiği gibi *yapışkan* davrandığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="393ab-600">Aşağıdaki kod için bir `/Zebra/Users/AddUser`URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="393ab-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="393ab-601">Eylem tanımı</span><span class="sxs-lookup"><span data-stu-id="393ab-601">Action definition</span></span>

<span data-ttu-id="393ab-602">[Eylem Dışı](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özelliği olanlar hariç, denetleyicideki ortak yöntemler eylemlerdir.</span><span class="sxs-lookup"><span data-stu-id="393ab-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="393ab-603">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="393ab-603">Sample code</span></span>

 * <span data-ttu-id="393ab-604">[MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) yöntemi [örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ye dahildir ve yönlendirme bilgilerini görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="393ab-605">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="393ab-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="393ab-606">ASP.NET Core MVC, gelen isteklerin URL'lerini eşleştirmek ve bunları eylemlerle eşleştirmek için Yönlendirme [aracını](xref:fundamentals/middleware/index) kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="393ab-607">Yollar başlangıç kodunda veya özniteliklerinde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="393ab-608">Rotalar, URL yollarının eylemlerle nasıl eşleşeceğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="393ab-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="393ab-609">Rotalar, yanıtlar olarak gönderilen URL'ler (bağlantılar için) oluşturmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="393ab-610">Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="393ab-611">Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="393ab-612">Daha fazla bilgi için [Karışık yönlendirmeye](#routing-mixed-ref-label) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="393ab-613">Bu belge, MVC ve yönlendirme arasındaki etkileşimleri ve tipik MVC uygulamalarının yönlendirme özelliklerinden nasıl yararlandığını açıklar.</span><span class="sxs-lookup"><span data-stu-id="393ab-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="393ab-614">Gelişmiş yönlendirme yle ilgili ayrıntılar için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="393ab-615">Yönlendirme Middleware'ini ayarlama</span><span class="sxs-lookup"><span data-stu-id="393ab-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="393ab-616">*Yapılandırma* yönteminizde şuna benzer bir kod görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="393ab-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="393ab-617">Çağrının `UseMvc`içinde, `MapRoute` `default` rota olarak adlandırılacağız tek bir rota oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="393ab-618">Çoğu MVC `default` uygulaması, rotaya benzer bir şablona sahip bir rota kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="393ab-619">Rota şablonu `"{controller=Home}/{action=Index}/{id?}"` gibi `/Products/Details/5` bir URL yolu eşleşebilir ve yolu belirterek rota değerlerini `{ controller = Products, action = Details, id = 5 }` ayıklar.</span><span class="sxs-lookup"><span data-stu-id="393ab-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="393ab-620">MVC adlı `ProductsController` bir denetleyici bulmak ve `Details`eylem çalıştırmak için çalışacağız:</span><span class="sxs-lookup"><span data-stu-id="393ab-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="393ab-621">Bu örnekte, model bağlama bu eylemi `id = 5` çağırırken `id` parametre `5` ayarlamak için değerini kullanacağını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="393ab-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="393ab-622">Daha fazla ayrıntı için [Model Bağlama'ya](../models/model-binding.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="393ab-623">Rotayı `default` kullanma:</span><span class="sxs-lookup"><span data-stu-id="393ab-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="393ab-624">Rota şablonu:</span><span class="sxs-lookup"><span data-stu-id="393ab-624">The route template:</span></span>

* <span data-ttu-id="393ab-625">`{controller=Home}`varsayılan `Home` olarak tanımlar`controller`</span><span class="sxs-lookup"><span data-stu-id="393ab-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="393ab-626">`{action=Index}`varsayılan `Index` olarak tanımlar`action`</span><span class="sxs-lookup"><span data-stu-id="393ab-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="393ab-627">`{id?}`isteğe `id` bağlı olarak tanımlar</span><span class="sxs-lookup"><span data-stu-id="393ab-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="393ab-628">Varsayılan ve isteğe bağlı rota parametrelerinin eşleşme için URL yolunda bulunması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="393ab-629">Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="393ab-630">`"{controller=Home}/{action=Index}/{id?}"`URL yolu `/` eşleşebilir ve rota `{ controller = Home, action = Index }`değerleri üretecek.</span><span class="sxs-lookup"><span data-stu-id="393ab-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="393ab-631">URL yolunda `controller` `action` karşılık gelen bir segment `id` olmadığından, varsayılan değerlerin değerleri ve varsayılan değerleri kullanma değerleri bir değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="393ab-632">MVC, eylemi `HomeController` ve eylemi `Index` seçmek için bu rota değerlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="393ab-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="393ab-633">Bu denetleyici tanımı ve yol `HomeController.Index` şablonu kullanılarak, eylem aşağıdaki URL yollarından herhangi biri için yürütülür:</span><span class="sxs-lookup"><span data-stu-id="393ab-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="393ab-634">Kolaylık yöntemi: `UseMvcWithDefaultRoute`</span><span class="sxs-lookup"><span data-stu-id="393ab-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="393ab-635">Değiştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="393ab-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="393ab-636">`UseMvc`ve `UseMvcWithDefaultRoute` ara yazılım `RouterMiddleware` boru hattına bir örnek ekleyin.</span><span class="sxs-lookup"><span data-stu-id="393ab-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="393ab-637">MVC ara yazılımlarla doğrudan etkileşime girmez ve istekleri işlemek için yönlendirme kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="393ab-638">MVC bir örneği ile yollara `MvcRouteHandler`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="393ab-639">İçindeki `UseMvc` kod aşağıdakilere benzer:</span><span class="sxs-lookup"><span data-stu-id="393ab-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="393ab-640">`UseMvc`doğrudan herhangi bir rota tanımlamaz, `attribute` rota için rota koleksiyonuna bir yer tutucu ekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="393ab-641">Aşırı yük, `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="393ab-642">`UseMvc`ve tüm varyasyonları öznitelik yolu için bir yer tutucu ekleyin - öznitelik yönlendirme nasıl `UseMvc`yapılandırırsanız yapılandırırsanız yapının her zaman kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="393ab-643">`UseMvcWithDefaultRoute`varsayılan bir rota tanımlar ve öznitelik yönlendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="393ab-644">[Öznitelik Yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="393ab-645">Konvansiyonel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-645">Conventional routing</span></span>

<span data-ttu-id="393ab-646">Rota: `default`</span><span class="sxs-lookup"><span data-stu-id="393ab-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="393ab-647">Önceki kod, geleneksel bir yönlendirme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="393ab-648">URL yolları için bir *kural kuralı* oluşturduğundan, bu stil geleneksel yönlendirme olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="393ab-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="393ab-649">İlk yol kesimi denetleyici adı eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="393ab-650">Eylem adının ikinci eşleri.</span><span class="sxs-lookup"><span data-stu-id="393ab-650">The second maps to the action name.</span></span>
* <span data-ttu-id="393ab-651">Üçüncü segment isteğe bağlı `id`olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="393ab-652">`id`bir model varlık için eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="393ab-653">`default` Bu yolu kullanarak, `/Products/List` URL yolu `ProductsController.List` eyleme `/Blog/Article/17` eşler ve `BlogController.Article`eşler.</span><span class="sxs-lookup"><span data-stu-id="393ab-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="393ab-654">Bu eşleme **yalnızca** denetleyici ve eylem adlarını temel alınarak ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel almamaktadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="393ab-655">Varsayılan rota ile geleneksel yönlendirme yi kullanmak, tanımladığınız her eylem için yeni bir URL deseni bulmak zorunda kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="393ab-656">CRUD tarzı eylemleri olan bir uygulama için, denetleyicilerinizdeki URL'ler için tutarlılık olması kodunuzu basitleştirmeye ve kullanıcı aranızı daha öngörülebilir hale getirmeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="393ab-657">Rota `id` şablonu tarafından isteğe bağlı olarak tanımlanır, bu da eylemlerinizin URL'nin bir parçası olarak sağlanan kimlik olmadan yürütülebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="393ab-658">URL'den atlanırsa genellikle ne olur, `id` model bağlama `0` tarafından ayarlanacak ve sonuç olarak veritabanı eşleşen `id == 0`hiçbir varlık bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="393ab-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="393ab-659">Öznitelik yönlendirme, bazı eylemler için gerekli olan kimliği yapmak için, diğerleri için değil, ince taneli denetim sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="393ab-660">Sözleşmeye göre belgeler, doğru `id` kullanımda görünme olasılıkları gibi isteğe bağlı parametreleri içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="393ab-661">Birden çok rota</span><span class="sxs-lookup"><span data-stu-id="393ab-661">Multiple routes</span></span>

<span data-ttu-id="393ab-662">Daha fazla çağrı `UseMvc` ekleyerek içine birden `MapRoute`çok yol ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="393ab-663">Bunu yapmak, birden çok kural tanımlamanıza veya belirli bir eyleme adanmış geleneksel rotalar eklemenize olanak tanır:</span><span class="sxs-lookup"><span data-stu-id="393ab-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="393ab-664">Buradaki `blog` rota özel bir *konvansiyonel rotadır,* yani geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme adanmıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="393ab-665">`controller` Rota `action` şablonunda parametre olarak görünmediğinden ve görünmediğinden, yalnızca varsayılan değerlere sahip olabilirler `BlogController.Article`ve bu nedenle bu yol her zaman eylemle eşlenecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="393ab-666">Rota koleksiyonundaki rotalar sipariş edilir ve eklendikleri sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="393ab-667">Bu örnekte, `blog` rota rotadan `default` önce denenecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-668">*Özel geleneksel rotalar* genellikle URL `{*article}` yolunun kalan kısmını yakalamak gibi **tüm yolları yakalama** parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="393ab-669">Bu, diğer rotalarla eşleşmek istediğiniz URL'lerle eşleştiğini anlamına gelen bir rotayı 'çok açgözlü' yapabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="393ab-670">Bunu çözmek için daha sonra rota tablosuna 'açgözlü' yolları koyun.</span><span class="sxs-lookup"><span data-stu-id="393ab-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="393ab-671">Geri dönüş</span><span class="sxs-lookup"><span data-stu-id="393ab-671">Fallback</span></span>

<span data-ttu-id="393ab-672">İstek işlemenin bir parçası olarak, MVC rota değerlerinin uygulamanızda bir denetleyici ve eylem bulmak için kullanılabileceğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="393ab-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="393ab-673">Rota değerleri bir eylemle eşleşmiyorsa, rota eşleşmez ve bir sonraki rota denenecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="393ab-674">Buna *geri dönüş*denir ve geleneksel yolların çakıştığı durumlarda basitleştirilmesi amaçlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="393ab-675">Disambiguating eylemleri</span><span class="sxs-lookup"><span data-stu-id="393ab-675">Disambiguating actions</span></span>

<span data-ttu-id="393ab-676">İki eylem yönlendirme yoluyla eşleştiğinde, MVC 'en iyi' adayı seçmek için ayrıştırmalı veya başka bir özel durum atmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="393ab-677">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="393ab-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="393ab-678">Bu denetleyici, URL yolu `/Products/Edit/17` ve rota verileri `{ controller = Products, action = Edit, id = 17 }`eşleşecek iki eylem tanımlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="393ab-679">Bu, bir ürünü düzenlemek için `Edit(int)` bir form gösterdiği ve `Edit(int, Product)` deftere nakledilen formu işlediği MVC denetleyicileri için tipik bir desendir.</span><span class="sxs-lookup"><span data-stu-id="393ab-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="393ab-680">Bunu mümkün kılmak için MVC `Edit(int, Product)` istek bir HTTP `POST` `Edit(int)` ve ne zaman HTTP fiil başka bir şey olduğunu seçmek gerekir.</span><span class="sxs-lookup"><span data-stu-id="393ab-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="393ab-681">`HttpPostAttribute` ( `[HttpPost]` ) ( ) `IActionConstraint` yalnızca http fiili . `POST`</span><span class="sxs-lookup"><span data-stu-id="393ab-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="393ab-682">Bir `IActionConstraint` varlığı `Edit(int, Product)` daha bir 'daha iyi' maç yapar `Edit(int)`, bu yüzden `Edit(int, Product)` ilk denenecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="393ab-683">Yalnızca özel senaryolarda `IActionConstraint` özel uygulamalar yazmanız gerekir, ancak benzer `HttpPostAttribute` öznitelikler diğer HTTP fiilleri için tanımlanır gibi özniteliklerin rolünü anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="393ab-684">Geleneksel yönlendirmede, eylemlerin `show form -> submit form` iş akışının bir parçası olduklarında aynı eylem adını kullanması yaygındır.</span><span class="sxs-lookup"><span data-stu-id="393ab-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="393ab-685">Bu desenin kolaylığı, [Anlama IActionConstraint](#understanding-iactionconstraint) bölümünü inceledikten sonra daha belirgin hale gelecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="393ab-686">Birden çok rota eşleşirse ve MVC 'en iyi' `AmbiguousActionException`rota bulamazsa, bir .</span><span class="sxs-lookup"><span data-stu-id="393ab-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="393ab-687">Rota adları</span><span class="sxs-lookup"><span data-stu-id="393ab-687">Route names</span></span>

<span data-ttu-id="393ab-688">Dizeleri `"blog"` ve `"default"` aşağıdaki örneklerde rota adları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="393ab-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="393ab-689">Rota adları, adlandırılmış rotanın URL üretimi için kullanılabileceğini görebilmek için rotaya mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="393ab-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="393ab-690">Bu, yolların sıralanması URL oluşturmayı karmaşık hale getirebildiği zaman URL oluşturmayı büyük ölçüde kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="393ab-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="393ab-691">Rota adları uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="393ab-692">Rota adlarının URL eşleştirmesi veya isteklerin işlenmesi üzerinde hiçbir etkisi yoktur; yalnızca URL üretimi için kullanılırlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="393ab-693">[Yönlendirme,](xref:fundamentals/routing) MVC'ye özel yardımcılarda URL üretimi de dahil olmak üzere URL oluşturma hakkında daha ayrıntılı bilgilere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="393ab-694">Öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-694">Attribute routing</span></span>

<span data-ttu-id="393ab-695">Öznitelik yönlendirme, eylemleri doğrudan rota şablonlarına yönlendirmek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="393ab-696">Aşağıdaki örnekte, `app.UseMvc();` `Configure` yöntem kullanılır ve hiçbir rota geçirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="393ab-697">Varsayılan `HomeController` rotanın `{controller=Home}/{action=Index}/{id?}` eşleşeceği URL'lerle eşleşecek:</span><span class="sxs-lookup"><span data-stu-id="393ab-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="393ab-698">Eylem, `HomeController.Index()` URL `/`yollarından herhangi biri `/Home`için `/Home/Index`yürütülecektir, veya .</span><span class="sxs-lookup"><span data-stu-id="393ab-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-699">Bu örnek, öznitelik yönlendirme ve geleneksel yönlendirme arasındaki önemli bir programlama farkVurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="393ab-700">Öznitelik yönlendirme, bir rota belirtmek için daha fazla giriş gerektirir; geleneksel varsayılan rota yolları daha kısa bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="393ab-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="393ab-701">Ancak, öznitelik yönlendirme, her eylem için hangi rota şablonlarının geçerli olduğunu niçin hassas bir şekilde denetlemeye izin verir (ve gerektirir).</span><span class="sxs-lookup"><span data-stu-id="393ab-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="393ab-702">Öznitelik yönlendirme ile denetleyici adı ve eylem adları hangi eylem seçilir **hiçbir** rol oynar.</span><span class="sxs-lookup"><span data-stu-id="393ab-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="393ab-703">Bu örnek, önceki örnekle aynı URL'lerle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="393ab-704">Yukarıdaki rota şablonları `action`, ve `area`. `controller`</span><span class="sxs-lookup"><span data-stu-id="393ab-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="393ab-705">Aslında, bu rota parametreleri öznitelik yollarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="393ab-706">Rota şablonu zaten bir eylemle ilişkili olduğundan, eylem adını URL'den ayrıştırmak mantıklı olmaz.</span><span class="sxs-lookup"><span data-stu-id="393ab-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="393ab-707">Http[Fiil] öznitelikleri ile öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="393ab-708">Öznitelik yönlendirme `Http[Verb]` si gibi `HttpPostAttribute`öznitelikleri de yararlanabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="393ab-709">Bu özniteliklerin tümü bir rota şablonu kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="393ab-710">Bu örnek, aynı rota şablonu eşleşen iki eylem gösterir:</span><span class="sxs-lookup"><span data-stu-id="393ab-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="393ab-711">Http fiili olduğunda `/products` `ProductsApi.ListProducts` `GET` eylem gibi bir URL yolu `ProductsApi.CreateProduct` yürütülür ve HTTP `POST`fiili .</span><span class="sxs-lookup"><span data-stu-id="393ab-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="393ab-712">Öznitelik yönlendirmesi ilk olarak URL ile rota öznitelikleri tarafından tanımlanan rota şablonları kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="393ab-713">Bir rota şablonu `IActionConstraint` eşleştikten sonra, hangi eylemlerin yürütülebileceğini belirlemek için kısıtlamalar uygulanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="393ab-714">Bir REST API'si inşa ederken, eylem `[Route(...)]` tüm HTTP yöntemlerini kabul edeceği için bir eylem yönteminde kullanmak isteyeceğiniz nadirdir.</span><span class="sxs-lookup"><span data-stu-id="393ab-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="393ab-715">API'nizin neyi desteklediği `Http*Verb*Attributes` konusunda kesin olmak için daha spesifik olanı kullanmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="393ab-716">REST API'lerinin istemcilerinin belirli mantıksal işlemlere hangi yolların ve HTTP fiillerinin eşlenebildiğini bilmeleri beklenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="393ab-717">Bir öznitelik rotası belirli bir eylem için geçerli olduğundan, rota şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="393ab-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="393ab-718">Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="393ab-719">Eylem `ProductsApi.GetProduct(int)` gibi `/products/3` bir URL yolu için yürütülür, `/products`ancak gibi bir URL yolu için değil.</span><span class="sxs-lookup"><span data-stu-id="393ab-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="393ab-720">Rota şablonlarının ve ilgili seçeneklerin tam açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="393ab-721">Rota Adı</span><span class="sxs-lookup"><span data-stu-id="393ab-721">Route Name</span></span>

<span data-ttu-id="393ab-722">Aşağıdaki kod bir *rota* adı `Products_List`tanımlar:</span><span class="sxs-lookup"><span data-stu-id="393ab-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="393ab-723">Rota adları, belirli bir rotayı temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="393ab-724">Rota adlarının yönlendirmenin URL eşleştirme davranışı üzerinde hiçbir etkisi yoktur ve yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="393ab-725">Rota adları uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-726">Bunu, parametreyi `id` isteğe bağlı olarak tanımlayan geleneksel`{id?}` *varsayılan rotayla*karşılaştırın ( ).</span><span class="sxs-lookup"><span data-stu-id="393ab-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="393ab-727">API'leri tam olarak belirtme becerisinin, `/products` farklı `/products/5` eylemlere izin verme ve gönderilme gibi avantajları vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="393ab-728">Yolları birleştirme</span><span class="sxs-lookup"><span data-stu-id="393ab-728">Combining routes</span></span>

<span data-ttu-id="393ab-729">Öznitelik yönlendirmeyi daha az yinelenen hale getirmek için denetleyicideki rota öznitelikleri, tek tek eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="393ab-730">Denetleyicide tanımlanan tüm rota şablonları, eylemlerdeki şablonları yönlendirmeye hazırlanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="393ab-731">Denetleyiciye bir rota özniteliği yerleştirmek, denetleyicideki **tüm** eylemleri öznitelik yönlendirmesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="393ab-732">Bu örnekte URL `/products` yolu `ProductsApi.ListProducts`eşleşebilir `/products/5` ve `ProductsApi.GetProduct(int)`URL yolu eşleşebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="393ab-733">Bu eylemlerin her ikisi `GET` de sadece HTTP `HttpGetAttribute`eşleşir, çünkü onlar ile işaretlenirler.</span><span class="sxs-lookup"><span data-stu-id="393ab-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="393ab-734">Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="393ab-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="393ab-735">Bu örnek, *varsayılan rotaya*benzer bir URL yolu kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="393ab-736">Öznitelik rotalarını sıralama</span><span class="sxs-lookup"><span data-stu-id="393ab-736">Ordering attribute routes</span></span>

<span data-ttu-id="393ab-737">Tanımlı bir sırada yürüten geleneksel yolların aksine, öznitelik yönlendirmesi bir ağaç oluşturur ve tüm rotalarla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="393ab-738">Bu, rota girişleri ideal bir sıralamaya yerleştirilmişse, bu şekilde olur; en özel yolları daha genel yolları önce yürütmek için bir şans var.</span><span class="sxs-lookup"><span data-stu-id="393ab-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="393ab-739">Örneğin, gibi `blog/search/{topic}` bir rota, .' `blog/{*article}`gibi bir rotadan daha belirgindir.</span><span class="sxs-lookup"><span data-stu-id="393ab-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="393ab-740">Mantıksal olarak `blog/search/{topic}` rota 'çalışır' ilk, varsayılan olarak, çünkü tek mantıklı sipariş konuşuyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="393ab-741">Geleneksel yönlendirmeyi kullanarak, geliştirici yolları istenilen sıraya yerleştirmekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="393ab-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="393ab-742">Öznitelik yolları, sağlanan tüm çerçevenin `Order` özelliğini kullanarak bir siparişi yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="393ab-743">Rotalar, artan bir `Order` özellik türüne göre işlenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="393ab-744">Varsayılan sıra. `0`</span><span class="sxs-lookup"><span data-stu-id="393ab-744">The default order is `0`.</span></span> <span data-ttu-id="393ab-745">Bir rotayı `Order = -1` kullanarak ayarlama, sipariş belirlemeyen rotalardan önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="393ab-746">Varsayılan rota `Order = 1` siparişinden sonra bir rota yı kullanmak çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="393ab-747">Bağlı olarak `Order`kaçının.</span><span class="sxs-lookup"><span data-stu-id="393ab-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="393ab-748">URL alanınız doğru yönlendirmek için açık sipariş değerleri gerektiriyorsa, istemciler için de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="393ab-749">Genel öznitelik yönlendirme, URL eşlemi ile doğru rotayı seçer.</span><span class="sxs-lookup"><span data-stu-id="393ab-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="393ab-750">URL oluşturma için kullanılan varsayılan sipariş çalışmıyorsa, rota adını geçersiz kılma olarak kullanmak `Order` genellikle özelliği uygulamaktan daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="393ab-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="393ab-751">Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="393ab-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="393ab-752">Razor Pages konularında rota sırası ile ilgili bilgiler [Razor Pages rota ve uygulama kurallarında mevcuttur: Rota sırası.](xref:razor-pages/razor-pages-conventions#route-order)</span><span class="sxs-lookup"><span data-stu-id="393ab-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="393ab-753">Rota şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])</span><span class="sxs-lookup"><span data-stu-id="393ab-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="393ab-754">Kolaylık sağlamak için, öznitelik rotaları, bir belirteç imal`[` `]`eden bir belirteç imal ederek *belirteç değiştirmeyi* destekler ( , ).</span><span class="sxs-lookup"><span data-stu-id="393ab-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="393ab-755">`[action]`Belirteçler, `[area]`ve `[controller]` rota tanımlandığı eylemeylem eylem eylem adı, alan adı ve denetleyici adı değerleri ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="393ab-756">Aşağıdaki örnekte, eylemler yorumlarda açıklandığı gibi URL yollarla eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="393ab-757">Belirteç değiştirme, öznitelik yollarını oluşturmanın son adımı olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="393ab-758">Yukarıdaki örnek aşağıdaki kodla aynı şekilde olacaktır:</span><span class="sxs-lookup"><span data-stu-id="393ab-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="393ab-759">Öznitelik yolları da devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="393ab-760">Bu özellikle belirteç değiştirme ile birlikte güçlüdür.</span><span class="sxs-lookup"><span data-stu-id="393ab-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="393ab-761">Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="393ab-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]`her eylem için benzersiz bir rota adı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="393ab-763">Gerçek belirteç değiştirme delimiter `[` `]`maç veya , karakter (veya)`[[` `]]`tekrarlayarak kaçmak.</span><span class="sxs-lookup"><span data-stu-id="393ab-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="393ab-764">Belirteç değiştirmesini özelleştirmek için parametre transformatörü kullanma</span><span class="sxs-lookup"><span data-stu-id="393ab-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="393ab-765">Belirteç değiştirme parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="393ab-766">Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="393ab-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="393ab-767">Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .</span><span class="sxs-lookup"><span data-stu-id="393ab-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="393ab-768">Bir `RouteTokenTransformerConvention` uygulama modeli kuralı:</span><span class="sxs-lookup"><span data-stu-id="393ab-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="393ab-769">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="393ab-770">Değiştirildikleri gibi öznitelik rotası belirteç değerlerini özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="393ab-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="393ab-771">Bir `RouteTokenTransformerConvention` seçenek olarak `ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="393ab-772">Birden Fazla Rota</span><span class="sxs-lookup"><span data-stu-id="393ab-772">Multiple Routes</span></span>

<span data-ttu-id="393ab-773">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yol tanımlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="393ab-774">Bunun en yaygın kullanımı, aşağıdaki örnekte gösterildiği gibi *varsayılan geleneksel rotanın* davranışını taklit etmektir:</span><span class="sxs-lookup"><span data-stu-id="393ab-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="393ab-775">Denetleyiciye birden çok yol öznitelikleri koymak, her birinin eylem yöntemlerindeki her bir rota öznitelikleriyle birleşeceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="393ab-776">Birden çok yol öznitelikleri (bu uygulama) `IActionConstraint`bir eylem ekikonulduğunda, her eylem kısıtlaması onu tanımlayan öznitelikten rota şablonuyla birleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="393ab-777">Eylemler üzerinde birden çok yol kullanmak güçlü görünse de, uygulamanızın URL alanını basit ve iyi tanımlanmış tutmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="393ab-778">Örneğin, varolan istemcileri desteklemek için yalnızca gerektiğinde eylemlerde birden çok yol kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="393ab-779">Öznitelik rotası isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtme</span><span class="sxs-lookup"><span data-stu-id="393ab-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="393ab-780">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır sıralı sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="393ab-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="393ab-781">Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="393ab-782">Özel rota öznitelikleri kullanarak`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="393ab-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="393ab-783">Çerçevede sağlanan tüm rota öznitelikleri (, `[Route(...)]`, `[HttpGet(...)]` `IRouteTemplateProvider` vb.) arabirimi uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="393ab-784">MVC, uygulama başlatıldığında denetleyici sınıfları ve eylem yöntemleriyle `IRouteTemplateProvider` ilgili öznitelikleri arar ve ilk rota kümesini oluşturmak için uygulayanları kullanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="393ab-785">Kendi rota `IRouteTemplateProvider` özniteliklerinizi tanımlamak için uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="393ab-786">Her `IRouteTemplateProvider` biri özel bir rota şablonu, sipariş ve ad ile tek bir rota tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="393ab-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="393ab-787">Yukarıdaki örnekteki öznitelik otomatik olarak `Template` `"api/[controller]"` ne `[MyApiController]` zaman uygulanacağı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="393ab-788">Öznitelik yollarını özelleştirmek için Uygulama Modelini kullanma</span><span class="sxs-lookup"><span data-stu-id="393ab-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="393ab-789">*Uygulama modeli,* mvc tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta verilerle başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="393ab-790">*Uygulama modeli,* rota özniteliklerinden (aracılığıyla) `IRouteTemplateProvider`toplanan tüm verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="393ab-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="393ab-791">Yönlendirmenin nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl olduğunu özelleştirmek için başlangıç zamanında uygulama modelini değiştirmek için *kurallar* yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="393ab-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="393ab-792">Bu bölümde, uygulama modelini kullanarak yönlendirme özelleştirme basit bir örnek gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="393ab-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="393ab-793">Karışık yönlendirme: Atfetme yönlendirme vs konvansiyonel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="393ab-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="393ab-794">MVC uygulamaları geleneksel yönlendirme ve öznitelik yönlendirme kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="393ab-795">Tarayıcılar için HTML sayfaları sunan denetleyiciler için geleneksel rotalar kullanmak ve REST API'leri sunan denetleyiciler için yönlendirme özelliği kullanmak tipiktir.</span><span class="sxs-lookup"><span data-stu-id="393ab-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="393ab-796">Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="393ab-797">Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="393ab-798">Öznitelik yollarını tanımlayan eylemlere geleneksel yollardan ve bunun tersi üzerinden ulaşılamıyor.</span><span class="sxs-lookup"><span data-stu-id="393ab-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="393ab-799">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemleri yönlendirilen yapar.</span><span class="sxs-lookup"><span data-stu-id="393ab-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-800">İki yönlendirme sistemi türünü diğerlerinden ayıran şey, bir URL rota şablonuyla eşleştikten sonra uygulanan işlemdir.</span><span class="sxs-lookup"><span data-stu-id="393ab-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="393ab-801">Geleneksel yönlendirmede, eşleşmedeki rota değerleri, tüm geleneksel yönlendirilmiş eylemlerin bir arama tablosundan eylem ve denetleyiciyi seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="393ab-802">Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilidir ve başka bir arama gerekmez.</span><span class="sxs-lookup"><span data-stu-id="393ab-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="393ab-803">Karmaşık segmentler</span><span class="sxs-lookup"><span data-stu-id="393ab-803">Complex segments</span></span>

<span data-ttu-id="393ab-804">Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]`), açgözlü olmayan bir şekilde sağdan sola literals eşleştirerek işlenir.</span><span class="sxs-lookup"><span data-stu-id="393ab-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="393ab-805">Açıklamanın [kaynak koduna](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="393ab-806">Daha fazla bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="393ab-807">URL Oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-807">URL Generation</span></span>

<span data-ttu-id="393ab-808">MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="393ab-809">URL'ler üretmek, kodlama URL'lerini ortadan kaldırarak kodunuzu daha sağlam ve korunabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="393ab-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="393ab-810">Bu bölüm, MVC tarafından sağlanan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="393ab-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="393ab-811">URL oluşturmanın ayrıntılı bir açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="393ab-812">Arabirim, `IUrlHelper` MVC ve URL üretimi için yönlendirme arasındaki altyapının altında yatan parçadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="393ab-813">Denetleyiciler, görünümler `IUrlHelper` ve görünüm `Url` bileşenleri nde özellik aracılığıyla kullanılabilir bir örnek bulacaksınız.</span><span class="sxs-lookup"><span data-stu-id="393ab-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="393ab-814">Bu örnekte, `IUrlHelper` arabirim `Controller.Url` başka bir eylem için bir URL oluşturmak için özellik üzerinden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="393ab-815">Uygulama varsayılan geleneksel rotayı kullanıyorsa, `url` değişkenin değeri URL `/UrlGeneration/Destination`yol dizesi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="393ab-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="393ab-816">Bu URL yolu, geçerli istekteki (ortam değerleri) rota değerlerini, geçirilen değerlerle birleştirerek `Url.Action` ve bu değerleri rota şablonuna ikame ederek yönlendirilerek oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="393ab-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="393ab-817">Rota şablonundaki her rota parametresinin değeri, adları değerler ve ortam değerleriyle eşleştirerek ikame edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="393ab-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="393ab-818">Değeri olmayan bir rota parametresi varsa varsayılan bir değer kullanabilir veya isteğe bağlıysa atlanabilir (bu `id` örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="393ab-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="393ab-819">Gerekli rota parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="393ab-820">URL oluşturma bir rota için başarısız olursa, sonraki rota tüm yollar denenene veya eşleşme bulunana kadar denendi.</span><span class="sxs-lookup"><span data-stu-id="393ab-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="393ab-821">`Url.Action` Yukarıdaki örnek, geleneksel yönlendirme yi varsayar, ancak URL oluşturma kavramları farklı olsa da öznitelik yönlendirmeile benzer şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="393ab-822">Geleneksel yönlendirme ile, rota değerleri bir şablonu genişletmek için kullanılır `controller` `action` ve rota değerleri için ve genellikle bu şablonda görünür - yönlendirme ile eşleşen URL'ler bir *kural kuralına*uygun olduğu için çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="393ab-823">Öznitelik yönlendirmesinde, rota değerlerinin `controller` `action` şablonda görünmesine izin verilmez ve bunun yerine hangi şablonun kullanılacağını aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="393ab-824">Bu örnek, öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="393ab-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="393ab-825">MVC, yönlendirilen tüm eylemlerin bir arama tablosu oluşturur `controller` `action` ve URL oluşturma için kullanılacak rota şablonu seçmek için ve değerleri eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="393ab-826">Yukarıdaki örnekte, `custom/url/to/destination` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="393ab-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="393ab-827">Eylem adına göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-827">Generating URLs by action name</span></span>

<span data-ttu-id="393ab-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="393ab-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="393ab-829">`Action`) ve ilgili tüm aşırı yüklemelerin tümü, bir denetleyici adı ve eylem adı belirterek neye bağlandığınızı belirtmek istediğiniz fikrine dayanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-830">Kullanırken, `Url.Action`geçerli rota `controller` değerleri `action` sizin için belirtilir ve `controller` `action` sizin için belirtilir - değeri ve hem *ortam değerleri* **ve** *değerlerinin*bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="393ab-831">Yöntem, `Url.Action`her zaman geçerli `action` değerleri `controller` kullanır ve geçerli eyleme yönlendirir bir URL yolu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="393ab-832">Yönlendirme, URL oluştururken sağlamadığınız bilgileri doldurmak için ortam değerlerindeki değerleri kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="393ab-833">Rota gibi `{a}/{b}/{c}/{d}` bir rota ve `{ a = Alice, b = Bob, c = Carol, d = David }`ortam değerleri kullanarak, yönlendirme, tüm rota parametrelerinin bir değeri olduğundan, ek değer içermeyen bir URL oluşturmak için yeterli bilgiye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="393ab-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="393ab-834">Değeri `{ d = Donovan }`eklediyseniz, değer `{ d = David }` yoksayılır ve oluşturulan URL yolu `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="393ab-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="393ab-835">URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="393ab-835">URL paths are hierarchical.</span></span> <span data-ttu-id="393ab-836">Yukarıdaki örnekte, değeri `{ c = Cheryl }`eklediyseniz, her iki `{ c = Carol, d = David }` değer de yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="393ab-837">Bu durumda artık bir değerimiz `d` kalmaz ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="393ab-838">İstenilen değeri belirtmeniz `c` `d`gerekir ve .</span><span class="sxs-lookup"><span data-stu-id="393ab-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="393ab-839">Bu sorunu varsayılan rotayla vurmayı`{controller}/{action}/{id?}`bekleyebilirsiniz ( ) - ancak her `Url.Action` zaman açıkça bir `controller` `action` ve değer belirteceği gibi uygulamada bu davranışla nadiren karşılaşırsınız.</span><span class="sxs-lookup"><span data-stu-id="393ab-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="393ab-840">Daha uzun `Url.Action` aşırı yükler, rota parametreleri dışındaki değerler sağlamak `controller` `action`için ek bir *rota değerleri* nesnesi de alır.</span><span class="sxs-lookup"><span data-stu-id="393ab-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="393ab-841">En sık bu gibi `id` `Url.Action("Buy", "Products", new { id = 17 })`kullanılır göreceksiniz .</span><span class="sxs-lookup"><span data-stu-id="393ab-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="393ab-842">Kural değeri nesnesi kural *değerlerine* göre genellikle anonim bir `IDictionary<>` nesnedir, ancak aynı zamanda eski bir *.NET nesnesi*de olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="393ab-843">Rota parametreleri ile eşleşmeyen ek rota değerleri sorgu dizesinde konur.</span><span class="sxs-lookup"><span data-stu-id="393ab-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="393ab-844">Mutlak bir URL oluşturmak için, bir: `protocol``Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="393ab-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="393ab-845">Rotaya göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-845">Generating URLs by route</span></span>

<span data-ttu-id="393ab-846">Yukarıdaki kod, denetleyici ve eylem adını geçerek bir URL oluşturmayı gösterdi.</span><span class="sxs-lookup"><span data-stu-id="393ab-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="393ab-847">`IUrlHelper`aynı zamanda `Url.RouteUrl` yöntem ailesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="393ab-848">Bu yöntemler `Url.Action`, ancak geçerli değerleri `action` ve `controller` rota değerlerine kopyalamak yok benzer.</span><span class="sxs-lookup"><span data-stu-id="393ab-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="393ab-849">En yaygın kullanım, genellikle bir denetleyici veya eylem adı *belirtmeden* URL'yi oluşturmak için belirli bir rotayı kullanmak üzere bir rota adı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="393ab-850">HTML'de URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-850">Generating URLs in HTML</span></span>

<span data-ttu-id="393ab-851">`IHtmlHelper``HtmlHelper` yöntemleri `Html.BeginForm` sağlar `Html.ActionLink` ve `<form>` oluşturmak `<a>` ve elemanları sırasıyla.</span><span class="sxs-lookup"><span data-stu-id="393ab-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="393ab-852">Bu yöntemler `Url.Action` bir URL oluşturmak için yöntemi kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="393ab-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="393ab-853">Bunun `Url.RouteUrl` için `HtmlHelper` refakatçive `Html.BeginRouteForm` `Html.RouteLink` benzer işlevsellik vardır.</span><span class="sxs-lookup"><span data-stu-id="393ab-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="393ab-854">`form` TagHelpers, TagHelper ve `<a>` TagHelper aracılığıyla URL'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="393ab-855">Bunların her `IUrlHelper` ikisi de bunların uygulanması için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="393ab-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="393ab-856">Daha fazla bilgi için [Formlarla Çalışma'ya](../views/working-with-forms.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="393ab-857">İç görünümler, `IUrlHelper` yukarıda `Url` yer almayan herhangi bir özel URL nesli için özellik üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="393ab-858">Eylem Sonuçlarında URL Oluşturma</span><span class="sxs-lookup"><span data-stu-id="393ab-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="393ab-859">Yukarıdaki örnekler denetleyicide `IUrlHelper` kullanmayı gösterirken, denetleyicideki en yaygın kullanım eylem sonucunun bir parçası olarak bir URL oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="393ab-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="393ab-860">Ve `ControllerBase` `Controller` temel sınıflar, başka bir eyleme başvuran eylem sonuçları için kolaylık yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="393ab-861">Tipik bir kullanım, kullanıcı girdisini kabul ettikten sonra yeniden yönlendirmektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="393ab-862">Eylem sonuçları fabrika yöntemleri üzerinde `IUrlHelper`yöntemlere benzer bir desen izleyin.</span><span class="sxs-lookup"><span data-stu-id="393ab-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="393ab-863">Özel konvansiyonel rotalar için özel durum</span><span class="sxs-lookup"><span data-stu-id="393ab-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="393ab-864">Konvansiyonel yönlendirme, özel bir *konvansiyonel rota*adı verilen özel bir rota tanımı türü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="393ab-865">Aşağıdaki örnekte, adlı `blog` rota özel bir geleneksel rotadır.</span><span class="sxs-lookup"><span data-stu-id="393ab-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="393ab-866">Bu rota tanımlarını `Url.Action("Index", "Home")` kullanarak, `default` rota `/` ile URL yolu oluşturur, ama neden?</span><span class="sxs-lookup"><span data-stu-id="393ab-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="393ab-867">Rota değerlerinin `{ controller = Home, action = Index }` bir URL oluşturmak `blog`için yeterli olacağını tahmin edebilirsiniz ve sonuç . `/blog?action=Index&controller=Home`</span><span class="sxs-lookup"><span data-stu-id="393ab-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="393ab-868">Özel geleneksel rotalar, rotanın URL oluşturmayla "çok açgözlü" olmasını engelleyen karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır.</span><span class="sxs-lookup"><span data-stu-id="393ab-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="393ab-869">Bu durumda varsayılan değerler `{ controller = Blog, action = Article }`, `controller` ve `action` ne ne de bir rota parametresi olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="393ab-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="393ab-870">Yönlendirme URL oluşturma gerçekleştirirken, sağlanan değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="393ab-871">Değerler `blog` `{ controller = Home, action = Index }` eşleşmediği `{ controller = Blog, action = Article }`için URL oluşturma kullanımı başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="393ab-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="393ab-872">Yönlendirme sonra denemek `default`için geri düşer , hangi başarılı.</span><span class="sxs-lookup"><span data-stu-id="393ab-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="393ab-873">Alanlar</span><span class="sxs-lookup"><span data-stu-id="393ab-873">Areas</span></span>

<span data-ttu-id="393ab-874">[Alanlar,](areas.md) ilgili işlevselliği ayrı bir yönlendirme ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir gruba düzenlemek için kullanılan bir MVC özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="393ab-875">Alanları kullanmak, farklı *alanlara*sahip oldukları sürece, bir uygulamanın aynı ada sahip birden çok denetleyiciye sahip olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="393ab-876">Alanları kullanmak, `area` başka bir rota parametresi ekleyerek yönlendirme amacıyla bir `controller` `action`hiyerarşi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="393ab-877">Bu bölümde yönlendirmenin alanlarla nasıl etkileşimde olduğu tartışılır - alanların görünümlerle nasıl kullanıldığıyla ilgili ayrıntılar için [Alanlar'a](areas.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="393ab-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="393ab-878">Aşağıdaki örnekte, Varsayılan geleneksel rotayı ve adlı bir alan `Blog`için alan *rotasını* kullanacak Şekilde MVC yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="393ab-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="393ab-879">Bir URL yolu `/Manage/Users/AddUser`gibi eşleştirirken, ilk rota `{ area = Blog, controller = Users, action = AddUser }`rota değerlerini üretecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="393ab-880">`area` Rota değeri için `area`varsayılan bir değer tarafından üretilen , aslında tarafından `MapAreaRoute` oluşturulan rota aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="393ab-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="393ab-881">`MapAreaRoute`bu durumda, `Blog`sağlanan alan adını kullanmak `area` için hem varsayılan değeri hem de kısıtlamayı kullanarak bir rota oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="393ab-882">Varsayılan değer, rotanın her `{ area = Blog, ... }`zaman üretir, kısıtlama `{ area = Blog, ... }` URL oluşturma için değer gerektirir sağlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="393ab-883">Konvansiyonel yönlendirme isteðe baðlð±r.</span><span class="sxs-lookup"><span data-stu-id="393ab-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="393ab-884">Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="393ab-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="393ab-885">Yukarıdaki örneği kullanarak, rota değerleri aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="393ab-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="393ab-886">Bir `AreaAttribute` denetleyiciyi bir alanın parçası olarak ifade eden şey, bu `Blog` denetleyicinin bölgede olduğunu söylüyoruz.</span><span class="sxs-lookup"><span data-stu-id="393ab-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="393ab-887">Özniteliği olmayan `[Area]` denetleyiciler herhangi bir alanın üyesi değildir ve `area` rota değeri yönlendirme ile sağlandığında **eşleşmez.**</span><span class="sxs-lookup"><span data-stu-id="393ab-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="393ab-888">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota `{ area = Blog, controller = Users, action = AddUser }`değerleriyle eşleşebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="393ab-889">Her denetleyicinin ad alanı burada tamolarak gösterilir - aksi takdirde denetleyicilerin bir adlandırma çakışması olur ve derleyici hatası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="393ab-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="393ab-890">Sınıf ad alanlarının MVC'nin yönlendirmesi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="393ab-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="393ab-891">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adları `area` rota değeri tarafından sağlandığında eşleşir.</span><span class="sxs-lookup"><span data-stu-id="393ab-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="393ab-892">Üçüncü denetleyici herhangi bir alanın üyesi değildir ve yalnızca yönlendirme `area` ile değer sağlanmadığı zaman eşleşebilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-893">Hiçbir *değer*eşleştirme açısından, `area` değer yokluğu için `area` değer null veya boş dize sanki aynıdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="393ab-894">Bir alan içinde bir eylem yürütürken, rota değeri `area` URL oluşturma için kullanılacak yönlendirme için bir ortam *değeri* olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="393ab-895">Bu, varsayılan olarak alanların URL oluşturma için aşağıdaki örnekte gösterildiği gibi *yapışkan* davrandığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="393ab-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="393ab-896">IActionConstraint'i Anlama</span><span class="sxs-lookup"><span data-stu-id="393ab-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="393ab-897">Bu bölüm, çerçeve içi ve MVC'nin yürütmek için nasıl bir eylem seçtiği konusunda derin bir dalıştır.</span><span class="sxs-lookup"><span data-stu-id="393ab-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="393ab-898">Tipik bir uygulamanın özel bir`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="393ab-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="393ab-899">Arabirimi bilmiyorsanız `IActionConstraint` bile büyük olasılıkla zaten kullanmışsınızdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="393ab-900">Öznitelik `[HttpGet]` ve `[Http-VERB]` benzeri `IActionConstraint` öznitelikleri bir eylem yönteminin yürütülmesini sınırlamak için uygular.</span><span class="sxs-lookup"><span data-stu-id="393ab-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="393ab-901">Varsayılan geleneksel rotayı varsayarsak, URL `/Products/Edit` `{ controller = Products, action = Edit }`yolu burada gösterilen eylemlerin **her ikisiyle de** eşleşen değerleri üretir.</span><span class="sxs-lookup"><span data-stu-id="393ab-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="393ab-902">Terminolojide, `IActionConstraint` bu eylemlerin her ikisinin de rota verileriyle eşleşeceği için aday olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="393ab-903">`HttpGetAttribute` Yürütüldüğünde, *Edit()* *GET* için bir maç olduğunu ve başka bir HTTP fiiliyle eşleşmediğini söyler.</span><span class="sxs-lookup"><span data-stu-id="393ab-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="393ab-904">Eylem `Edit(...)` tanımlanan herhangi bir kısıtlama yok ve bu yüzden herhangi bir HTTP fiil eşleşecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="393ab-905">Yani varsayarak - `POST` sadece `Edit(...)` maçlar.</span><span class="sxs-lookup"><span data-stu-id="393ab-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="393ab-906">Ancak, her `GET` iki eylem için de hala eşleşebilir - ancak, bir `IActionConstraint` eylem her zaman olmadan bir eylem daha *iyi* olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="393ab-907">Bu `Edit()` nedenle `[HttpGet]` daha spesifik olarak kabul edilir ve her iki eylem eşleşebilir eğer seçilecektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="393ab-908">Kavramsal olarak, `IActionConstraint` aşırı *yükleme*biçimidir, ancak aynı ada sahip aşırı yükleme yöntemleri yerine, aynı URL ile eşleşen eylemler arasında aşırı yükleme dir.</span><span class="sxs-lookup"><span data-stu-id="393ab-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="393ab-909">Öznitelik yönlendirme de `IActionConstraint` kullanır ve farklı denetleyicileri her ikisi de aday olarak kabul edilen eylemleri neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="393ab-910">IActionConstraint'in Uygulanması</span><span class="sxs-lookup"><span data-stu-id="393ab-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="393ab-911">Bir uygulamayı `IActionConstraint` uygulamanın en basit yolu, `System.Attribute` türetilen bir sınıf oluşturmak ve bunu eylemlerinizin ve denetleyicilerinizin üzerine yerleştirmektir.</span><span class="sxs-lookup"><span data-stu-id="393ab-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="393ab-912">MVC, öznitelik `IActionConstraint` olarak uygulanan ları otomatik olarak keşfeder.</span><span class="sxs-lookup"><span data-stu-id="393ab-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="393ab-913">Kısıtlamaları uygulamak için uygulama modelini kullanabilirsiniz ve bu, bunların nasıl uygulandığını meta programlamanıza olanak sağladığından büyük olasılıkla en esnek yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="393ab-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="393ab-914">Aşağıdaki örnekte, bir kısıtlama rota verilerinden bir *ülke koduna* dayalı bir eylem seçer.</span><span class="sxs-lookup"><span data-stu-id="393ab-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="393ab-915">[GitHub tam örnek](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="393ab-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="393ab-916">Yöntemi uygulamaktan ve `Accept` kısıtlamanın yürütülmesi için bir 'Sipariş' seçmekten siz sorumlusunuz.</span><span class="sxs-lookup"><span data-stu-id="393ab-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="393ab-917">Bu durumda, `Accept` eylemi `true` belirtmek için yöntem döner `country` rota değeri eşleştiğinde bir eşleşmedir.</span><span class="sxs-lookup"><span data-stu-id="393ab-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="393ab-918">Bu, atfedilmeyen bir eyleme geri dönüşe izin verdiği açısından farklıdır. `RouteValueAttribute`</span><span class="sxs-lookup"><span data-stu-id="393ab-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="393ab-919">Örnek, bir `en-US` eylem tanımlarsanız, benzer `fr-FR` bir ülke kodunun uygulanmamış `[CountrySpecific(...)]` daha genel bir denetleyiciye geri döneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="393ab-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="393ab-920">Özellik, `Order` kısıtlamanın hangi *aşamanın* parçası olduğuna karar verir.</span><span class="sxs-lookup"><span data-stu-id="393ab-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="393ab-921">Eylem kısıtlamaları gruplar halinde `Order`çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="393ab-922">Örneğin, http yöntemi öznitelikleri sağlanan çerçevenin `Order` tümü aynı aşamada çalıştırmak için aynı değeri kullanın.</span><span class="sxs-lookup"><span data-stu-id="393ab-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="393ab-923">İstediğiniz ilkeleri uygulamak için gereken kadar aşamanız olabilir.</span><span class="sxs-lookup"><span data-stu-id="393ab-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="393ab-924">Kısıtlamanızın HTTP yöntemlerine önce uygulanıp uygulanmayacağı hakkında düşünmek için `Order` bir değer eve karar vermek.</span><span class="sxs-lookup"><span data-stu-id="393ab-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="393ab-925">Önce daha düşük sayılar çalışır.</span><span class="sxs-lookup"><span data-stu-id="393ab-925">Lower numbers run first.</span></span>

::: moniker-end
