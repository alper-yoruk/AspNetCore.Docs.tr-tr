---
title: ASP.NET Core denetleyici eylemlerine yönlendirme
author: rick-anderson
description: ASP.NET Core MVC 'nin, gelen isteklerin URL 'Lerini eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme ara yazılımını nasıl kullandığını öğrenin.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: a163c87fdb9a02c1b074ab32c19c11932c66cfd4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854541"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="ca2f9-103">ASP.NET Core denetleyici eylemlerine yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="ca2f9-104">[Ryan şimdi ak](https://github.com/rynowak), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca2f9-105">ASP.NET Core denetleyicileri, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları [eylemlerle](#action)eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="ca2f9-106">Rota şablonları:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-106">Routes templates:</span></span>

* <span data-ttu-id="ca2f9-107">, Başlangıç kodunda veya özniteliklerde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="ca2f9-108">URL yollarının [eylemlerle](#action)nasıl eşleştiğini betimleyen.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="ca2f9-109">Bağlantıların URL 'Leri oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="ca2f9-110">Oluşturulan bağlantılar genellikle yanıtlar halinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="ca2f9-111">Eylemler [genel olarak-yönlendirildi](#cr) veya [Attribute-yönlendirildi](#ar)' dir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="ca2f9-112">Bir yolun denetleyiciye veya [eyleme](#action) yerleştirilmesi, BT özniteliğinin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="ca2f9-113">Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ca2f9-114">Bu belge:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-114">This document:</span></span>

* <span data-ttu-id="ca2f9-115">MVC ve yönlendirme arasındaki etkileşimleri açıklar:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="ca2f9-116">Tipik MVC uygulamalarının yönlendirme özelliklerini kullanma şekli.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="ca2f9-117">Her ikisini de içerir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-117">Covers both:</span></span>
    * <span data-ttu-id="ca2f9-118">Genellikle denetleyiciler ve görünümlerle kullanılan [genel olarak yönlendirme](#cr) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="ca2f9-119">REST API 'Leri ile kullanılan *öznitelik yönlendirme* .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="ca2f9-120">Birincil olarak REST API 'Leri için yönlendirme ile ilgileniyorsanız, [REST API 'leri Için öznitelik yönlendirme](#ar) bölümüne atlayın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="ca2f9-121">Bkz. Gelişmiş yönlendirme ayrıntıları için [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="ca2f9-122">ASP.NET Core 3,0 ' de eklenen varsayılan yönlendirme sisteminin Endpoint Routing olarak adlandırıldığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="ca2f9-123">Uyumluluk amaçlarıyla önceki yönlendirme sürümü ile denetleyicileri kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="ca2f9-124">Yönergeler için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="ca2f9-125">Eski yönlendirme sistemindeki başvuru malzemeleri için [Bu belgenin 2,2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="ca2f9-126">Geleneksel rotayı ayarlama</span><span class="sxs-lookup"><span data-stu-id="ca2f9-126">Set up conventional route</span></span>

<span data-ttu-id="ca2f9-127">`Startup.Configure` genellikle [geleneksel yönlendirme](#crd)kullanılırken aşağıdakine benzer bir kod içerir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="ca2f9-128">Çağrısı içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> tek bir yol oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="ca2f9-129">Tek yol yol olarak adlandırılır `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-129">The single route is named `default` route.</span></span> <span data-ttu-id="ca2f9-130">Denetleyiciler ve görünümler içeren çoğu uygulama, yola benzer bir rota şablonu kullanır `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="ca2f9-131">REST API 'Leri [öznitelik yönlendirmeyi](#ar)kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="ca2f9-132">Yol şablonu `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="ca2f9-133">Şöyle bir URL yoluyla eşleşir `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="ca2f9-134">`{ controller = Products, action = Details, id = 5 }`Yolu simgeleştirileyerek yol değerlerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ca2f9-135">Uygulamanın adlı bir denetleyici ve bir eylem varsa yol değerlerinin ayıklanması bir eşleşme ile sonuçlanır `ProductsController` `Details` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="ca2f9-136">`/Products/Details/5` model, `id = 5` parametresini olarak ayarlamak için değerini bağlar `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="ca2f9-137">Daha fazla ayrıntı için bkz. [model bağlama](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="ca2f9-138">`{controller=Home}``Home`Varsayılan olarak tanımlar `controller` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="ca2f9-139">`{action=Index}``Index`Varsayılan olarak tanımlar `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="ca2f9-140">`?`İçindeki karakter, `{id?}` `id` isteğe bağlı olarak tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="ca2f9-141">Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ca2f9-142">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="ca2f9-143">URL yoluyla eşleşir `/` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="ca2f9-144">Yol değerlerini üretir `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="ca2f9-145">İçin değerleri `controller` ve `action` varsayılan değerleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="ca2f9-146">`id` URL yolunda karşılık gelen bir kesim olmadığından değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ca2f9-147">`/` yalnızca bir ve eylemi varsa eşleşir `HomeController` `Index` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ca2f9-148">Önceki denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yolları için çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="ca2f9-149">URL yolu, `/` yönlendirme şablonu varsayılan `Home` denetleyicilerini ve eylemini kullanır `Index` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="ca2f9-150">URL yolu, `/Home` yönlendirme şablonu varsayılan eylemini kullanır `Index` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="ca2f9-151">Kolaylık yöntemi <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="ca2f9-152">Yerine</span><span class="sxs-lookup"><span data-stu-id="ca2f9-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="ca2f9-153">Yönlendirme, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ara yazılımı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="ca2f9-154">Denetleyicileri kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-154">To use controllers:</span></span>
>
> * <span data-ttu-id="ca2f9-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` [Öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için içinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="ca2f9-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> Hem [genel olarak yönlendirmeli](#cr) denetleyicileri hem de [öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için veya öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ca2f9-157">Geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-157">Conventional routing</span></span>

<span data-ttu-id="ca2f9-158">Geleneksel yönlendirme, denetleyiciler ve görünümlerle kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="ca2f9-159">`default`Yol:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="ca2f9-160">, *geleneksel yönlendirmeye* bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="ca2f9-161">Bu, URL yolları için bir *kural* oluşturduğundan *geleneksel yönlendirme* olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ca2f9-162">İlk yol segmenti, `{controller=Home}` , denetleyici adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="ca2f9-163">İkinci kesim, `{action=Index}` , [eylem](#action) adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="ca2f9-164">Üçüncü segment, `{id?}` isteğe bağlı olarak kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="ca2f9-165">`?`İçinde, `{id?}` isteğe bağlı yapar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="ca2f9-166">`id` bir model varlığına eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="ca2f9-167">Bu `default` yolu kullanarak, URL yolu:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="ca2f9-168">`/Products/List``ProductsController.List`eyleme eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="ca2f9-169">`/Blog/Article/17` ile eşlenir `BlogController.Article` ve model genellikle `id` parametresini 17 ' ye bağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="ca2f9-170">Bu eşleme:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-170">This mapping:</span></span>

* <span data-ttu-id="ca2f9-171">**Yalnızca** denetleyiciyi ve [eylem](#action) adlarını temel alır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="ca2f9-172">Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel alan değildir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="ca2f9-173">Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, her eylem için yeni bir URL düzeniyle karşılaşmadan uygulamanın oluşturulmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="ca2f9-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) stilinde eylemlere sahip bir uygulama için, denetleyiciler arasında URL 'ler için tutarlılık vardır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="ca2f9-175">Kodu basitleştirmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-175">Helps simplify the code.</span></span>
* <span data-ttu-id="ca2f9-176">Kullanıcı arabirimini daha öngörülebilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ca2f9-177">`id`Önceki kodda, yol şablonu tarafından isteğe bağlı olarak tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="ca2f9-178">Eylemler, URL 'nin bir parçası olarak belirtilen isteğe bağlı KIMLIK olmadan çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="ca2f9-179">Genellikle, `id` URL 'den atlandığında:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="ca2f9-180">`id` , `0` model bağlama tarafından olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="ca2f9-181">Veritabanında eşleşen bir varlık bulunamadı `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="ca2f9-182">[Öznitelik yönlendirme](#ar) , kimliği bazı eylemler için gerekli hale getirmek için ayrıntılı denetim sağlar ve diğerleri için değildir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ca2f9-183">Kurala göre belgeler, `id` doğru kullanımlarda görünebilecekleri gibi isteğe bağlı parametreler içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="ca2f9-184">Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="ca2f9-185">Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="ca2f9-186">Temel ve açıklayıcı bir yönlendirme düzenini destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="ca2f9-187">, UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="ca2f9-188">Birçok Web UI uygulaması için tek yol şablonu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="ca2f9-189">Daha büyük Web UI uygulamaları için, [alan](#areas) kullanan başka bir yol da sıkça gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="ca2f9-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="ca2f9-191">, Çağrıldığı sıraya göre kendi uç noktalarına otomatik olarak bir **sipariş** değeri atayın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="ca2f9-192">ASP.NET Core 3,0 ve üzeri için uç nokta yönlendirme:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="ca2f9-193">Bir yol kavramı yoktur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="ca2f9-194">, Genişletilebilirlik yürütülmesi için sıralama garantisi sağlamaz, tüm uç noktalar bir kerede işlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="ca2f9-195">İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının nasıl yapıldığını görmek için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin <xref:Microsoft.AspNetCore.Routing.Route> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="ca2f9-196">[Öznitelik yönlendirme](#ar) bu belgenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="ca2f9-197">Birden çok geleneksel yollar</span><span class="sxs-lookup"><span data-stu-id="ca2f9-197">Multiple conventional routes</span></span>

<span data-ttu-id="ca2f9-198">[](#cr) `UseEndpoints` Ve ' ye daha fazla çağrı eklenerek, içine birden çok geleneksel yol eklenebilir <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="ca2f9-199">Bunun yapılması, birden çok kural tanımlamayı veya belirli bir [eyleme](#action)adanmış geleneksel yollar eklemeyi sağlar; örneğin:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="ca2f9-200">`blog`Önceki koddaki yol, **ayrılmış bir geleneksel yoldur**.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="ca2f9-201">Ayrılmış bir geleneksel yol olarak adlandırılır çünkü:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="ca2f9-202">[Geleneksel yönlendirme](#cr)kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="ca2f9-203">Belirli bir [eyleme](#action)ayrılmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="ca2f9-204">`controller`Ve `action` yol şablonunda parametre olarak görünmediği için `"blog/{*article}"` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="ca2f9-205">Yalnızca varsayılan değerleri olabilir `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="ca2f9-206">Bu yol her zaman eyleme eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ca2f9-207">`/Blog`, `/Blog/Article` ve, `/Blog/{any-string}` Blog rotası ile eşleşen tek URL yollarıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="ca2f9-208">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-208">The preceding example:</span></span>

* <span data-ttu-id="ca2f9-209">`blog` yol, önce eklendiğinden, rotadan eşleşme için daha yüksek önceliğe sahiptir `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="ca2f9-210">, URL 'nin bir parçası olarak bir makale adının olması gereken tipik bir [başlık](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="ca2f9-211">ASP.NET Core 3,0 ve üzeri sürümlerde yönlendirme:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="ca2f9-212">*Yol* adlı bir kavram tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-212">Define a concept called a *route*.</span></span> <span data-ttu-id="ca2f9-213">`UseRouting` ara yazılım ardışık düzenine eşleşen rota ekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="ca2f9-214">`UseRouting`Ara yazılım, uygulamada tanımlanan uç noktalar kümesine bakar ve isteğe bağlı olarak en iyi uç nokta eşleşmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="ca2f9-215">Veya gibi genişletilebilirlik yürütme sırası hakkında garanti sağlar <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="ca2f9-216">Bkz. yönlendirme ile ilgili başvuru malzemeleri için [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="ca2f9-217">Geleneksel yönlendirme sırası</span><span class="sxs-lookup"><span data-stu-id="ca2f9-217">Conventional routing order</span></span>

<span data-ttu-id="ca2f9-218">Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyicinin bir bileşimiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="ca2f9-219">Bu, geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="ca2f9-220">,, Ve kullanarak yollar ekleme, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> çağırdıkları sıraya göre bitiş noktalarına otomatik olarak bir sipariş değeri atar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="ca2f9-221">Daha önce görüntülenen bir rotadaki eşleşmelerin önceliği daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="ca2f9-222">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca2f9-223">Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="ca2f9-224">Catch-all yol parametrelerine sahip [adanmış geleneksel yollar](#dcr) `{*article}` , bir yol çok uzun sürebilir [](xref:fundamentals/routing#greedy), yani diğer yollarla eşleştirmek istediğiniz URL 'lerle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ca2f9-225">Doyumsuz yollarını daha sonra yol tablosuna yerleştirerek doyumsuz eşleşmelerini önleyin.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="ca2f9-226">Belirsiz eylemleri çözme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="ca2f9-227">Yönlendirme ile iki uç nokta eşleşmesi durumunda, yönlendirme aşağıdakilerden birini yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="ca2f9-228">En iyi aday ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-228">Choose the best candidate.</span></span>
* <span data-ttu-id="ca2f9-229">Bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-229">Throw an exception.</span></span>

<span data-ttu-id="ca2f9-230">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="ca2f9-231">Yukarıdaki denetleyici, eşleşen iki eylemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="ca2f9-232">URL yolu `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="ca2f9-233">Veri yönlendirin `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="ca2f9-234">Bu, MVC denetleyicileri için tipik bir modeldir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="ca2f9-235">`Edit(int)` bir ürünü düzenlemek için bir form görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="ca2f9-236">`Edit(int, Product)` Postalanan formu işler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="ca2f9-237">Doğru yolu çözümlemek için:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-237">To resolve the correct route:</span></span>

* <span data-ttu-id="ca2f9-238">`Edit(int, Product)` istek bir HTTP olduğunda seçilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="ca2f9-239">`Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="ca2f9-240">`Edit(int)` genellikle aracılığıyla çağrılır `GET` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="ca2f9-241">, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` İsteğin HTTP yöntemine göre seçim yapabilmesi için yönlendirme için verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="ca2f9-242">, `HttpPostAttribute` `Edit(int, Product)` Daha iyi bir eşleşme yapar `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="ca2f9-243">Gibi özniteliklerin rolünü anlamanız önemlidir `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="ca2f9-244">Benzer öznitelikler diğer [http fiilleri](#verb)için tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="ca2f9-245">[Geleneksel yönlendirmesinde](#cr), bir görüntüleme formu, gönder formu iş akışının bir parçası olduklarında aynı eylem adını kullanmak yaygın olarak kullanılan bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="ca2f9-246">Örneğin, bkz. [Iki düzenleme eylemi yöntemini İnceleme](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="ca2f9-247">Yönlendirme bir en iyi aday seçebilirse, <xref:System.Reflection.AmbiguousMatchException> birden fazla eşleşen uç noktayı listeleyerek bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="ca2f9-248">Geleneksel yol adları</span><span class="sxs-lookup"><span data-stu-id="ca2f9-248">Conventional route names</span></span>

<span data-ttu-id="ca2f9-249">Dizeler  `"blog"` ve `"default"` Aşağıdaki örneklerde geleneksel yol adları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-250">Yol adları, yola mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-250">The route names give the route a logical name.</span></span> <span data-ttu-id="ca2f9-251">Adlandırılmış yol, URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="ca2f9-252">Adlandırılmış bir yol kullanmak, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ca2f9-253">Yol adları benzersiz uygulama genelinde olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="ca2f9-254">Yol adları:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-254">Route names:</span></span>

* <span data-ttu-id="ca2f9-255">İsteklerin URL 'siyle eşleşmesi veya işlenmesi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="ca2f9-256">Yalnızca URL oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-256">Are used only for URL generation.</span></span>

<span data-ttu-id="ca2f9-257">Yol adı kavramı [ıendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)olarak yönlendirme ile temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="ca2f9-258">Terimler **yol adı** ve **uç nokta adı**:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="ca2f9-259">Değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-259">Are interchangeable.</span></span>
* <span data-ttu-id="ca2f9-260">Belgede kullanılan ve kod, açıklanan API 'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="ca2f9-261">REST API 'Leri için öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="ca2f9-262">REST API 'Leri, uygulamanın işlevselliğini [http fiilleri](#verb)tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="ca2f9-263">Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ca2f9-264">Aşağıdaki `StartUp.Configure` kod, bir REST API için tipik bir sonraki örnekte kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="ca2f9-265">Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` öznitelik yönlendirmeli denetleyicileri eşlemek için içinde çağırılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="ca2f9-266">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-266">In the following example:</span></span>

* <span data-ttu-id="ca2f9-267">Önceki `Configure` yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="ca2f9-268">`HomeController` Varsayılan geleneksel yolun eşleşdiküyle benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="ca2f9-269">Eylem,, `HomeController.Index` veya URL yollarından herhangi biri için çalıştırılır `/` `/Home` `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="ca2f9-270">Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasında bir temel programlama farkı vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="ca2f9-271">Öznitelik yönlendirme için bir yol belirtmek için daha fazla giriş gerekir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="ca2f9-272">Geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ca2f9-273">Ancak, öznitelik yönlendirme izin verir ve her [eyleme](#action)hangi rota şablonlarının uygulanacağını kesin olarak denetler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="ca2f9-274">Öznitelik yönlendirme ile, denetleyici ve eylem adları, [belirteç değiştirme](#routing-token-replacement-templates-ref-label) kullanılmadığı takdirde, eylemin eşleştirildiği hiçbir parçasını oynar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="ca2f9-275">Aşağıdaki örnek, önceki örnekle aynı URL 'Lerle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ca2f9-276">Aşağıdaki kod, ve için belirteç yerini `action` kullanır `controller` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="ca2f9-277">Aşağıdaki kod `[Route("[controller]/[action]")]` Denetleyici için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ca2f9-278">Yukarıdaki kodda, `Index` Yöntem şablonlarının sonuna `/` veya yol şablonlarına eklenmiş olması gerekir `~/` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="ca2f9-279">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="ca2f9-280">Rota şablonu seçimi hakkında bilgi için bkz. [route Template önceliği](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="ca2f9-281">Ayrılmış yönlendirme adları</span><span class="sxs-lookup"><span data-stu-id="ca2f9-281">Reserved routing names</span></span>

<span data-ttu-id="ca2f9-282">Aşağıdaki anahtar sözcükler, denetleyiciler veya sayfalar kullanılırken ayrılmış yol parametresi adlarıdır Razor :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="ca2f9-283">`page`Öznitelik yönlendirme ile yol parametresi olarak kullanmak yaygın bir hatadır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="ca2f9-284">Bunun yapılması, URL oluşturma ile tutarsız ve kafa karıştırıcı davranışa neden olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="ca2f9-285">Özel parametre adları URL oluşturma tarafından bir URL oluşturma işleminin bir sayfaya mı yoksa bir denetleyiciye mi başvurduğunu belirlemekte kullanılır Razor .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="ca2f9-286">HTTP fiili şablonları</span><span class="sxs-lookup"><span data-stu-id="ca2f9-286">HTTP verb templates</span></span>

<span data-ttu-id="ca2f9-287">ASP.NET Core aşağıdaki HTTP fiili şablonlarına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="ca2f9-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="ca2f9-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="ca2f9-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="ca2f9-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="ca2f9-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="ca2f9-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="ca2f9-294">Rota şablonları</span><span class="sxs-lookup"><span data-stu-id="ca2f9-294">Route templates</span></span>

<span data-ttu-id="ca2f9-295">ASP.NET Core aşağıdaki yol şablonlarına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="ca2f9-296">Tüm [http fiili şablonları](#verb) rota şablonlarıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="ca2f9-297">[Yolu](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca2f9-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="ca2f9-298">Http fiili öznitelikleriyle öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="ca2f9-299">Aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ca2f9-300">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-300">In the preceding code:</span></span>

* <span data-ttu-id="ca2f9-301">Her eylem `[HttpGet]` yalnızca http get istekleriyle eşleştirmeyi kısıtlayan özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="ca2f9-302">`GetProduct`Eylem, şablonu içerir `"{id}"` , bu nedenle `id` `"api/[controller]"` denetleyicideki şablonun sonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="ca2f9-303">Yöntemler şablonu `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="ca2f9-304">Bu nedenle bu eylem yalnızca `/api/test2/xyz` ,, `/api/test2/123` vb. için get istekleri ile eşleşir `/api/test2/{any string}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="ca2f9-305">`GetIntProduct`Eylem `"int/{id:int}")` şablonu içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="ca2f9-306">`:int`Şablonun kısmı, `id` yol değerlerini bir tamsayıya dönüştürülebileceği dizelere kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="ca2f9-307">Bir GET isteği `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="ca2f9-308">Bu eylemle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="ca2f9-309">Bir [404 bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hatası döndürür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="ca2f9-310">`GetInt2Product`Eylem şablonda bulunur `{id}` , ancak `id` bir tamsayıya dönüştürülemeyen değerlerle sınırlandırmaz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="ca2f9-311">Bir GET isteği `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="ca2f9-312">Bu rota ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-312">Matches this route.</span></span>
  * <span data-ttu-id="ca2f9-313">Model bağlama `abc` bir tamsayıya dönüştürülemez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="ca2f9-314">`id`Yönteminin parametresi tamsayıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="ca2f9-315">Model bağlama bir tamsayıya dönüştürülemediğinden, [400 hatalı bir istek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür `abc` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="ca2f9-316">Öznitelik yönlendirme,, <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> ve gibi öznitelikleri <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> kullanabilir <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="ca2f9-317">Tüm [http fiili](#verb) öznitelikleri bir yol şablonunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="ca2f9-318">Aşağıdaki örnekte, aynı yol şablonuyla eşleşen iki eylem gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="ca2f9-319">URL yolu kullanılarak `/products3` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="ca2f9-320">`MyProductsController.ListProducts`Eylem [http fiili](#verb) olduğunda çalışır `GET` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="ca2f9-321">`MyProductsController.CreateProduct`Eylem [http fiili](#verb) olduğunda çalışır `POST` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="ca2f9-322">Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul ettiğinden bir eylem yönteminde kullanmanız gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="ca2f9-323">API 'nizin neleri desteklediği hakkında kesin olması için, daha özel [http fiili özniteliği](#verb) kullanmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="ca2f9-324">REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ca2f9-325">REST API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="ca2f9-326">Bu, örneğin, aynı mantıksal kaynaktaki al ve postala gibi birçok işlemin aynı URL 'YI kullanması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="ca2f9-327">Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="ca2f9-328">Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ca2f9-329">Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ca2f9-330">`Products2ApiController.GetProduct(int)`Eylem:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="ca2f9-331">, Şunun gibi URL yoluyla çalıştırılır `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="ca2f9-332">URL yoluyla çalıştırılmadı `/products2` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="ca2f9-333">[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="ca2f9-334">Daha fazla bilgi için bkz. [desteklenen istek içerik türlerini tüketir özniteliğiyle tanımlama](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="ca2f9-335">Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="ca2f9-336">Hakkında daha fazla bilgi için `[ApiController]` bkz. [Apicontroller özniteliği](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="ca2f9-337">Yönlendirme adı</span><span class="sxs-lookup"><span data-stu-id="ca2f9-337">Route name</span></span>

<span data-ttu-id="ca2f9-338">Aşağıdaki kod, bir yol adı tanımlar `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ca2f9-339">Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ca2f9-340">Yol adları:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-340">Route names:</span></span>

* <span data-ttu-id="ca2f9-341">Yönlendirmenin URL eşleşen davranışı üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="ca2f9-342">Yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-342">Are only used for URL generation.</span></span>

<span data-ttu-id="ca2f9-343">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ca2f9-344">Önceki kodu, `id` parametreyi isteğe bağlı () olarak tanımlayan geleneksel varsayılan rotayla kontrast `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ca2f9-345">API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="ca2f9-346">Öznitelik yollarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-346">Combining attribute routes</span></span>

<span data-ttu-id="ca2f9-347">Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ca2f9-348">Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ca2f9-349">Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="ca2f9-350">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-350">In the preceding example:</span></span>

* <span data-ttu-id="ca2f9-351">URL yolu `/products` eşleşiyor `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="ca2f9-352">URL yolu `/products/5` eşleşiyor `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="ca2f9-353">Bu eylemlerin her ikisi de, özniteliğiyle işaretlendiğinden yalnızca HTTP ile eşleşir `GET` `[HttpGet]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="ca2f9-354">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ca2f9-355">Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolları kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="ca2f9-356">Aşağıdaki tabloda `[Route]` önceki koddaki öznitelikler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="ca2f9-357">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="ca2f9-357">Attribute</span></span>               | <span data-ttu-id="ca2f9-358">İle birleştirir `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="ca2f9-359">Rota şablonunu tanımlar</span><span class="sxs-lookup"><span data-stu-id="ca2f9-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="ca2f9-360">Yes</span><span class="sxs-lookup"><span data-stu-id="ca2f9-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="ca2f9-361">Yes</span><span class="sxs-lookup"><span data-stu-id="ca2f9-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="ca2f9-362">**Hayır**</span><span class="sxs-lookup"><span data-stu-id="ca2f9-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="ca2f9-363">Yes</span><span class="sxs-lookup"><span data-stu-id="ca2f9-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="ca2f9-364">Öznitelik yolu sırası</span><span class="sxs-lookup"><span data-stu-id="ca2f9-364">Attribute route order</span></span>

<span data-ttu-id="ca2f9-365">Yönlendirme bir ağaç oluşturur ve aynı anda tüm uç noktaları eşleştirir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="ca2f9-366">Yol girdileri ideal bir sıralamaya yerleştirilmiş gibi davranır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="ca2f9-367">En özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ca2f9-368">Örneğin, gibi bir öznitelik yolu, `blog/search/{topic}` gibi bir öznitelik rotasına göre daha özgüdür `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="ca2f9-369">`blog/search/{topic}`Daha belirgin olduğundan, yolun önceliği daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="ca2f9-370">[Geleneksel yönlendirmeyi](#cr)kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ca2f9-371">Öznitelik yolları özelliği kullanarak bir sıra yapılandırabilir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="ca2f9-372">Tüm Framework 'ün [yol öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dahil edilmiştir `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="ca2f9-373">Yollar, özelliğin artan sıralamasına göre işlenir `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ca2f9-374">Varsayılan sıra `0` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-374">The default order is `0`.</span></span> <span data-ttu-id="ca2f9-375">Bir `Order = -1` siparişi ayarlamadığı rotalardan önce çalıştırmaları kullanarak bir rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="ca2f9-376">`Order = 1`Varsayılan yol sıralaması sonrasında çalıştırmaları kullanarak bir rota ayarlama.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="ca2f9-377"> Uygulamasına bağlı kaçının `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="ca2f9-378">Bir uygulamanın URL 'SI alanı, doğru şekilde yönlendirmek için açık sıra değerleri gerektiriyorsa, bu durumda istemciler de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ca2f9-379">Genel olarak, öznitelik yönlendirme URL ile eşleşen doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ca2f9-380">URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, geçersiz kılma olarak bir yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ca2f9-381">Her ikisi de eşleşen yolu tanımlayan aşağıdaki iki denetleyicisi göz önünde bulundurun `/home` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ca2f9-382">`/home`Yukarıdaki kodla istemek şuna benzer bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="ca2f9-383">`Order`Yol özniteliklerinden birine ekleme belirsizliği çözer:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="ca2f9-384">Yukarıdaki kodla `/home` `HomeController.Index` bitiş noktasını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="ca2f9-385">Öğesine ulaşmak için `MyDemoController.MyIndex` `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="ca2f9-386">**Note**:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-386">**Note**:</span></span>

* <span data-ttu-id="ca2f9-387">Yukarıdaki kod bir örnek veya kötü yönlendirme tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="ca2f9-388">Özelliği göstermek için kullanılmıştı `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="ca2f9-389">`Order`Özelliği yalnızca belirsizlik çözümleniyor, bu şablon eşleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="ca2f9-390">Şablonu kaldırmak daha iyi olacaktır `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="ca2f9-391">Bkz. [ Razor sayfa yönlendirme ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) sayfalarla rota sıralaması hakkında bilgi için rota sırası Razor .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="ca2f9-392">Bazı durumlarda, belirsiz yollarla bir HTTP 500 hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="ca2f9-393">Hangi uç noktaların neden olduğunu görmek için [günlük kaydını](xref:fundamentals/logging/index) kullanın `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ca2f9-394">Yönlendirme şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]</span><span class="sxs-lookup"><span data-stu-id="ca2f9-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="ca2f9-395">Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (,) içine alarak *belirteç değiştirmeyi* destekler `[` `]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-395">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="ca2f9-396">, Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-396">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ca2f9-397">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-397">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="ca2f9-398">Eşleştir `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-398">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="ca2f9-399">Eşleştir `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-399">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="ca2f9-400">Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-400">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ca2f9-401">Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-401">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="ca2f9-402">Öznitelik rotaları de devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-402">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ca2f9-403">Bu, belirteç değiştirme ile güçlü bir şekilde birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-403">This is powerful combined with token replacement.</span></span> <span data-ttu-id="ca2f9-404">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-404">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="ca2f9-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="ca2f9-406">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-406">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="ca2f9-407">Her eylem için benzersiz bir yol adı üretir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-407">generates a unique route name for each action.</span></span>

<span data-ttu-id="ca2f9-408">Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya  `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-408">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ca2f9-409">Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="ca2f9-409">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ca2f9-410">Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-410">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ca2f9-411">Bir parametre transformatörü <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-411">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="ca2f9-412">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini şu şekilde değiştirir `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-412">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="ca2f9-413">, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Şu şekilde bir uygulama modeli kuralıdır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-413">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="ca2f9-414">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-414">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ca2f9-415">Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-415">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="ca2f9-416">Önceki `ListAll` Yöntem eşleşir `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-416">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="ca2f9-417">, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-417">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="ca2f9-418">Bilgi tanımı için bkz. [Mdıdn Web docs](https://developer.mozilla.org/docs/Glossary/Slug) for the başlık.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-418">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="ca2f9-419">Birden çok öznitelik yolu</span><span class="sxs-lookup"><span data-stu-id="ca2f9-419">Multiple attribute routes</span></span>

<span data-ttu-id="ca2f9-420">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-420">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ca2f9-421">Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel yolun davranışını taklit etmek olur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-421">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="ca2f9-422">Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biri ile birleştiribileceği anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-422">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="ca2f9-423">Tüm [http fiili](#verb) yol kısıtlamaları uygulanır `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-423">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="ca2f9-424">Uygulayan birden çok yol özniteliği <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> bir eyleme yerleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-424">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="ca2f9-425">Her eylem kısıtlaması, denetleyiciye uygulanan rota şablonuyla birlikte birleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-425">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="ca2f9-426">Eylemlerde birden çok yolun kullanılması yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmanız daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-426">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="ca2f9-427">**Yalnızca** gerektiğinde, var olan istemcileri desteklemek için, eylemlerde birden çok yol kullanın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-427">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ca2f9-428">Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-428">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ca2f9-429">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-429">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="ca2f9-430">Yukarıdaki kodda, `[HttpPost("product/{id:int}")]` bir yol kısıtlaması uygular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-430">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="ca2f9-431">`ProductsController.ShowProduct`Eylem yalnızca, gıbı URL yollarıyla eşleştirilir `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-431">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="ca2f9-432">Yol şablonu bölümü, `{id:int}` Bu segmenti yalnızca tamsayılarla kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-432">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="ca2f9-433">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-433">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ca2f9-434">Iroutetemplateprovider kullanarak özel yol öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="ca2f9-434">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="ca2f9-435">Tüm [Rota öznitelikleri](#rt) uygular <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-435">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="ca2f9-436">ASP.NET Core çalışma zamanı:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-436">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="ca2f9-437">Uygulama başladığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-437">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="ca2f9-438">`IRouteTemplateProvider`İlk yol kümesini oluşturmak için uygulayan öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-438">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ca2f9-439">`IRouteTemplateProvider`Özel yol özniteliklerini tanımlamak için uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-439">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="ca2f9-440">Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-440">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="ca2f9-441">Önceki `Get` Yöntem döndürülür `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-441">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ca2f9-442">Öznitelik yollarını özelleştirmek için uygulama modelini kullanın</span><span class="sxs-lookup"><span data-stu-id="ca2f9-442">Use application model to customize attribute routes</span></span>

<span data-ttu-id="ca2f9-443">Uygulama modeli:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-443">The application model:</span></span>

* <span data-ttu-id="ca2f9-444">, Başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-444">Is an object model created at startup.</span></span>
* <span data-ttu-id="ca2f9-445">Bir uygulamadaki eylemleri yönlendirmek ve yürütmek için ASP.NET Core tarafından kullanılan tüm meta verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-445">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="ca2f9-446">Uygulama modeli, yol özniteliklerinden toplanan tüm verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-446">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="ca2f9-447">Yol özniteliklerinin verileri uygulama tarafından sağlanır `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-447">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="ca2f9-448">Adlandır</span><span class="sxs-lookup"><span data-stu-id="ca2f9-448">Conventions:</span></span>

* <span data-ttu-id="ca2f9-449">, Yönlendirmenin nasıl davranacağını özelleştirmek için uygulama modelini değiştirmek üzere yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-449">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="ca2f9-450">Uygulama başlangıcında okundu.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-450">Are read at app startup.</span></span>

<span data-ttu-id="ca2f9-451">Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin temel bir örneği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-451">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="ca2f9-452">Aşağıdaki kod, rotaları projenin klasör yapısıyla kabaca bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-452">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="ca2f9-453">Aşağıdaki kod, `namespace` kuralı yönlendirilen denetleyicilere uygulanmasını engeller:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-453">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="ca2f9-454">Örneğin, aşağıdaki denetleyici kullanmaz `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-454">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ca2f9-455">`NamespaceRoutingConvention.Apply`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-455">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="ca2f9-456">Denetleyici öznitelik yönlendirmemişse hiçbir şey yapmaz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-456">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="ca2f9-457">Temel kaldırılmış olarak, denetleyici şablonunu öğesine göre ayarlar `namespace` `namespace` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-457">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="ca2f9-458">, `NamespaceRoutingConvention` ' De uygulanabilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-458">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="ca2f9-459">Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-459">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="ca2f9-460">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-460">In the preceding code:</span></span>

* <span data-ttu-id="ca2f9-461">Temel `namespace` `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-461">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="ca2f9-462">Önceki denetleyicinin tam adı `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-462">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="ca2f9-463">, `NamespaceRoutingConvention` Denetleyiciler şablonunu olarak ayarlar `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-463">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="ca2f9-464">, `NamespaceRoutingConvention` Bir denetleyiciye bir öznitelik olarak da uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-464">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ca2f9-465">Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-465">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ca2f9-466">ASP.NET Core uygulamalar geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-466">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ca2f9-467">Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-467">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ca2f9-468">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-468">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca2f9-469">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-469">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca2f9-470">Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-470">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ca2f9-471">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki **Tüm** eylemlerin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-471">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="ca2f9-472">Öznitelik yönlendirme ve geleneksel yönlendirme aynı yönlendirme altyapısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-472">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="ca2f9-473">URL oluşturma ve ortam değerleri</span><span class="sxs-lookup"><span data-stu-id="ca2f9-473">URL Generation and ambient values</span></span>

<span data-ttu-id="ca2f9-474">Uygulamalar, eylemlere URL bağlantıları oluşturmak için yönlendirme URL 'SI oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-474">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ca2f9-475">URL 'Leri oluşturmak, kod daha sağlam ve sürdürülebilir hale getirmek için sorunsuz kodlama URL 'Lerini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-475">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="ca2f9-476">Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturma özelliğinin nasıl çalıştığına ilişkin temel bilgileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-476">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="ca2f9-477">URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-477">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ca2f9-478"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki altyapının temel öğesidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-478">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ca2f9-479">Bir örneği, `IUrlHelper` `Url` denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-479">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ca2f9-480">Aşağıdaki örnekte, `IUrlHelper` arabirimi `Controller.Url` başka bir eyleme bir URL oluşturmak için özelliği aracılığıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-480">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-481">Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkeninin DEĞERI URL yol dizesidir `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-481">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ca2f9-482">Bu URL yolu, yönlendirme tarafından birleştirerek oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-482">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="ca2f9-483">Geçerli istekten, **ortam değerleri** olarak adlandırılan rota değerleri.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-483">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="ca2f9-484">`Url.Action`Bu değerleri yol şablonuna geçirilen ve yerine geçen değerler:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-484">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ca2f9-485">Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-485">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ca2f9-486">Bir değere sahip olmayan bir rota parametresi şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-486">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="ca2f9-487">Bir varsayılan değer varsa, bu değeri kullanın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-487">Use a default value if it has one.</span></span>
* <span data-ttu-id="ca2f9-488">İsteğe bağlı ise atlanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-488">Be skipped if it's optional.</span></span> <span data-ttu-id="ca2f9-489">Örneğin, `id` Rota şablonundan `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-489">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="ca2f9-490">Herhangi bir gerekli yol parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-490">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ca2f9-491">Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-491">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ca2f9-492">Yukarıdaki örnekte `Url.Action` [geleneksel yönlendirme](#cr)varsayılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-492">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="ca2f9-493">URL oluşturma, [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır, ancak kavramlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-493">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="ca2f9-494">Geleneksel yönlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-494">With conventional routing:</span></span>

* <span data-ttu-id="ca2f9-495">Rota değerleri bir şablonu genişletmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-495">The route values are used to expand a template.</span></span>
* <span data-ttu-id="ca2f9-496">İçin yol değerleri `controller` ve `action` genellikle bu şablonda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-496">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="ca2f9-497">Bu, yönlendirme ile eşleşen URL 'Ler bir kurala bağlı olduğundan, bu işe yarar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-497">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="ca2f9-498">Aşağıdaki örnek öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-498">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-499">`Source`Yukarıdaki koddaki eylem oluşturulur `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-499">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="ca2f9-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> ASP.NET Core 3,0 ' ye alternatif olarak eklenmiştir `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="ca2f9-501">`LinkGenerator` benzer ancak daha esnek işlevler sunar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-501">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="ca2f9-502">Üzerindeki her `IUrlHelper` bir yöntemi, buna karşılık gelen bir yöntem ailesini `LinkGenerator` de içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-502">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ca2f9-503">Eylem adına göre URL 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-503">Generating URLs by action name</span></span>

<span data-ttu-id="ca2f9-504">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirtilerek hedef uç noktası oluşturmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-504">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="ca2f9-505">Kullanırken `Url.Action` , ve için geçerli yol değerleri `controller` `action` çalışma zamanı tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-505">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="ca2f9-506">`controller`Ve değeri `action` hem [ortam değerlerinin](#ambient) hem de değerlerinin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-506">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="ca2f9-507">Yöntemi `Url.Action` her zaman ve geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-507">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="ca2f9-508">Yönlendirme, bir URL oluştururken sağlanmayan bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-508">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="ca2f9-509">Ortam değerleriyle benzer bir yol düşünün `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-509">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="ca2f9-510">Yönlendirmenin ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-510">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="ca2f9-511">Tüm rota parametrelerinin bir değeri olduğundan, yönlendirmeye yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-511">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="ca2f9-512">Değer `{ d = Donovan }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-512">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="ca2f9-513">Değer `{ d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-513">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="ca2f9-514">Oluşturulan URL yolu `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-514">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="ca2f9-515">**Uyarı**: URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-515">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="ca2f9-516">Yukarıdaki örnekte, değer `{ c = Cheryl }` eklenirse:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-516">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="ca2f9-517">Her iki değer de `{ c = Carol, d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-517">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="ca2f9-518">Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-518">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="ca2f9-519">İçin istenen değerleri `c` ve `d` bir URL oluşturmak için belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-519">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="ca2f9-520">Varsayılan yol ile bu soruna isabet etmeniz gerekebilir `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-520">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="ca2f9-521">`Url.Action`Her zaman açıkça bir ve değeri belirttiğinden bu sorun pratikte nadir olarak belirlenir `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-521">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="ca2f9-522">URL 'nin çeşitli aşırı yüklemeleri [. eylem](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) , ve dışındaki rota parametreleri için değerler sağlamak üzere bir yol değerleri nesnesi alır `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-522">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ca2f9-523">Route Values nesnesi genellikle ile birlikte kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-523">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="ca2f9-524">Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-524">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ca2f9-525">Yol değerleri nesnesi:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-525">The route values object:</span></span>

* <span data-ttu-id="ca2f9-526">Kural gereği genellikle anonim türdeki bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-526">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="ca2f9-527">Bir `IDictionary<>` veya [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-527">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="ca2f9-528">Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-528">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ca2f9-529">Yukarıdaki kod oluşturulur `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-529">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="ca2f9-530">Aşağıdaki kod mutlak URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-530">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="ca2f9-531">Mutlak URL oluşturmak için, aşağıdakilerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-531">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="ca2f9-532">Kabul eden bir aşırı yükleme `protocol` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-532">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="ca2f9-533">Örneğin, yukarıdaki kod.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-533">For example, the preceding code.</span></span>
* <span data-ttu-id="ca2f9-534">Varsayılan olarak mutlak URI 'Ler üreten [Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-534">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="ca2f9-535">Yola göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-535">Generate URLs by route</span></span>

<span data-ttu-id="ca2f9-536">Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-536">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ca2f9-537">`IUrlHelper` Ayrıca [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) ailesi yöntemlerin de sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-537">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="ca2f9-538">Bu yöntemler [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)ile benzerdir, ancak geçerli değerlerini `action` ve `controller` Rota değerlerine kopyalamaz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-538">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ca2f9-539">En yaygın kullanımı `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-539">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="ca2f9-540">URL oluşturmak için bir yol adı belirtir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-540">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="ca2f9-541">Genellikle bir denetleyici veya eylem adı belirtmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-541">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-542">Aşağıdaki Razor Dosya, için BIR HTML bağlantısı oluşturur `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-542">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="ca2f9-543">HTML ve, URL 'Leri oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="ca2f9-543">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="ca2f9-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>sırasıyla ve öğeleri oluşturmak Için [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini sağlar `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ca2f9-545">Bu yöntemler bir URL oluşturmak için [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-545">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ca2f9-546">`Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-546">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ca2f9-547">Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-547">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ca2f9-548">Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-548">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ca2f9-549">Daha fazla bilgi için bkz. [Formlardaki etiket yardımcıları](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-549">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="ca2f9-550">Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-550">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="ca2f9-551">Eylem sonuçlarında URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-551">URL generation in Action Results</span></span>

<span data-ttu-id="ca2f9-552">Yukarıdaki örneklerde `IUrlHelper` bir denetleyicide kullanılması gösterildi.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-552">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="ca2f9-553">Bir denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak bir URL oluşturmak olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-553">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ca2f9-554"><xref:Microsoft.AspNetCore.Mvc.ControllerBase>Ve <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-554">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ca2f9-555">Kullanıcı girişini kabul ettikten sonra, yaygın olarak kullanılan bir kullanım yeniden yönlendirilmelidir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-555">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="ca2f9-556">Eylem, gibi Fabrika yöntemleri sonuçları <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-556">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ca2f9-557">Adanmış geleneksel yollar için özel durum</span><span class="sxs-lookup"><span data-stu-id="ca2f9-557">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ca2f9-558">[Geleneksel yönlendirme](#cr) , [adanmış geleneksel yol](#dcr)olarak adlandırılan özel bir yol tanımı türünü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-558">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="ca2f9-559">Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-559">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-560">Önceki yol tanımlarını kullanarak, `Url.Action("Index", "Home")` yolu kullanarak URL yolunu oluşturur `/` `default` , ancak neden?</span><span class="sxs-lookup"><span data-stu-id="ca2f9-560">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="ca2f9-561">Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-561">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ca2f9-562">[Adanmış geleneksel yollar](#dcr) , karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır ve bu da yol, URL oluşturma ile çok fazla [dodan](xref:fundamentals/routing#greedy) yararlanın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-562">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="ca2f9-563">Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-563">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ca2f9-564">Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-564">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ca2f9-565">`blog`Değerler EŞLEŞMEDIĞINDEN URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-565">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ca2f9-566">Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-566">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ca2f9-567">Alanlar</span><span class="sxs-lookup"><span data-stu-id="ca2f9-567">Areas</span></span>

<span data-ttu-id="ca2f9-568">[Bölgeler](xref:mvc/controllers/areas) , ilgili işlevselliği ayrı olarak bir grup içinde düzenlemek için kullanılan bir MVC özelliğidir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-568">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="ca2f9-569">Denetleyici eylemleri için yönlendirme ad alanı.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-569">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="ca2f9-570">Görünümler için klasör yapısı.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-570">Folder structure for views.</span></span>

<span data-ttu-id="ca2f9-571">Alanların kullanılması, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-571">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="ca2f9-572">Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-572">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ca2f9-573">Bu bölümde yönlendirmenin alanlarla nasıl etkileşim kurduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-573">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="ca2f9-574">Alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](xref:mvc/controllers/areas) bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-574">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="ca2f9-575">Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve bir adlandırılmış yolu kullanacak şekilde yapılandırır `area` `area` `Blog` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-575">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ca2f9-576">Önceki kodda, öğesini <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> oluşturmak için çağırılır `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-576">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="ca2f9-577">İkinci parametresi, `"Blog"` , alan adıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-577">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="ca2f9-578">Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` yol, `"blog_route"` yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-578">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ca2f9-579">`area`Rota değeri, için varsayılan bir değer tarafından üretilir `area` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-579">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="ca2f9-580">Tarafından oluşturulan yol `MapAreaControllerRoute` , aşağıdaki ile eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-580">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="ca2f9-581">`MapAreaControllerRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-581">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ca2f9-582">Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-582">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="ca2f9-583">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-583">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca2f9-584">Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-584">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ca2f9-585">Önceki örneği kullanarak, yol değerleri `{ area = Blog, controller = Users, action = AddUser }` aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-585">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ca2f9-586">[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir alanın parçası olarak denetleyiciyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-586">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="ca2f9-587">Bu denetleyici `Blog` alanında bulunur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-587">This controller is in the `Blog` area.</span></span> <span data-ttu-id="ca2f9-588">Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir ve  `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-588">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ca2f9-589">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-589">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="ca2f9-590">Her denetleyicinin ad alanı, tamamlanma açısından burada gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-590">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="ca2f9-591">Yukarıdaki denetleyiciler aynı ad alanını kullanıyorsa, bir derleyici hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-591">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="ca2f9-592">Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-592">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ca2f9-593">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-593">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ca2f9-594">Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-594">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="ca2f9-595">Değer *olmadan* eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-595">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ca2f9-596">Bir alan içinde bir eylem yürütürken, için rota değeri, `area` yönlendirme için, URL oluşturma için kullanılacak bir [çevresel değer](#ambient) olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-596">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="ca2f9-597">Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-597">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="ca2f9-598">Aşağıdaki kod, için bir URL oluşturur `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-598">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="ca2f9-599">Eylem tanımı</span><span class="sxs-lookup"><span data-stu-id="ca2f9-599">Action definition</span></span>

<span data-ttu-id="ca2f9-600">Bir denetleyicide, [Nonactıon](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özniteliğine sahip olanlar hariç genel yöntemler eylemlerdir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-600">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ca2f9-601">Örnek kod</span><span class="sxs-lookup"><span data-stu-id="ca2f9-601">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="ca2f9-602">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca2f9-602">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca2f9-603">ASP.NET Core MVC, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-603">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="ca2f9-604">Yollar başlangıç kodunda veya özniteliklerde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-604">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="ca2f9-605">Yollar URL yollarının eylemlerle nasıl eşleştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-605">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="ca2f9-606">Yollar, yanıt olarak gönderilen URL 'Leri (bağlantılar için) oluşturmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-606">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="ca2f9-607">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-607">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca2f9-608">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-608">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca2f9-609">Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-609">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ca2f9-610">Bu belge, MVC ve yönlendirme arasındaki etkileşimleri ve tipik MVC uygulamalarının yönlendirme özelliklerini nasıl kullandığını açıklar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-610">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="ca2f9-611">Gelişmiş yönlendirme hakkında ayrıntılar için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-611">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="ca2f9-612">Yönlendirme ara yazılımını ayarlama</span><span class="sxs-lookup"><span data-stu-id="ca2f9-612">Setting up Routing Middleware</span></span>

<span data-ttu-id="ca2f9-613">*Yapılandırma* yönteminde şuna benzer bir kod görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-613">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca2f9-614">Çağrısı içinde `UseMvc` , `MapRoute` yolu olarak başvurabileceğiniz tek bir yol oluşturmak için kullanılır `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-614">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="ca2f9-615">Çoğu MVC uygulaması, yola benzer bir şablon içeren bir yol kullanır `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-615">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="ca2f9-616">Yol şablonu, `"{controller=Home}/{action=Index}/{id?}"` gibi BIR URL yoluyla eşleşir `/Products/Details/5` ve `{ controller = Products, action = Details, id = 5 }` yolu simgeleştirileyerek yol değerlerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-616">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ca2f9-617">MVC adlı bir denetleyiciyi bulmayı dener ve bu `ProductsController` eylemi çalıştırır `Details` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-617">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="ca2f9-618">Bu örnekte, model bağlamanın `id = 5` `id` Bu eylemi çağırırken parametresini olarak ayarlamak için değerini kullanacağı unutulmamalıdır `5` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-618">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="ca2f9-619">Daha fazla ayrıntı için [model bağlamaya](../models/model-binding.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-619">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="ca2f9-620">Yolu kullanarak `default` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-620">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ca2f9-621">Yol şablonu:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-621">The route template:</span></span>

* <span data-ttu-id="ca2f9-622">`{controller=Home}``Home`Varsayılan olarak tanımlar`controller`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-622">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="ca2f9-623">`{action=Index}``Index`Varsayılan olarak tanımlar`action`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-623">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="ca2f9-624">`{id?}``id`isteğe bağlı olarak tanımlar</span><span class="sxs-lookup"><span data-stu-id="ca2f9-624">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="ca2f9-625">Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-625">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ca2f9-626">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-626">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="ca2f9-627">`"{controller=Home}/{action=Index}/{id?}"` URL yoluyla eşleştirebilir `/` ve yol değerlerini oluşturacaktır `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-627">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="ca2f9-628">, `controller` `action` `id` URL yolunda karşılık gelen bir kesim olmadığından, için değerleri ve varsayılan değerleri kullanır, bir değer üretmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-628">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ca2f9-629">MVC, ve eylemini seçmek için bu yol değerlerini `HomeController` kullanır `Index` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-629">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ca2f9-630">Bu denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yollarından herhangi biri için yürütülür:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-630">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="ca2f9-631">Kolaylık yöntemi `UseMvcWithDefaultRoute` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-631">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="ca2f9-632">Değiştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-632">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca2f9-633">`UseMvc` ve `UseMvcWithDefaultRoute` `RouterMiddleware` , ara yazılım ardışık düzenine bir örneği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-633">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="ca2f9-634">MVC, doğrudan ara yazılım ile etkileşime girmez ve istekleri işlemek için yönlendirmeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-634">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="ca2f9-635">MVC, bir örneği aracılığıyla yollara bağlanır `MvcRouteHandler` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-635">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="ca2f9-636">İçindeki kod `UseMvc` Şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-636">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="ca2f9-637">`UseMvc` hiçbir yol doğrudan tanımlamaz, yol için yol koleksiyonuna bir yer tutucu ekler `attribute` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-637">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="ca2f9-638">Aşırı yükleme `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-638">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="ca2f9-639">`UseMvc` ve tüm varyasyonları, yapılandırma şeklinden bağımsız olarak her zaman için öznitelik rotası özniteliği yönlendirme özelliği için bir yer tutucu ekler `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-639">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="ca2f9-640">`UseMvcWithDefaultRoute` Varsayılan bir yol tanımlar ve öznitelik yönlendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-640">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="ca2f9-641">[Öznitelik yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-641">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ca2f9-642">Geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-642">Conventional routing</span></span>

<span data-ttu-id="ca2f9-643">`default`Yol:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-643">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ca2f9-644">Yukarıdaki kod geleneksel yönlendirmeye bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-644">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="ca2f9-645">Bu stil, URL yolları için bir *kural* oluşturduğundan geleneksel yönlendirme olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-645">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ca2f9-646">İlk yol segmenti, denetleyicinin adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-646">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="ca2f9-647">İkincisi eylem adıyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-647">The second maps to the action name.</span></span>
* <span data-ttu-id="ca2f9-648">Üçüncü segment, isteğe bağlı olarak kullanılır `id` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-648">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="ca2f9-649">`id` bir model varlığına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-649">`id` maps to a model entity.</span></span>

<span data-ttu-id="ca2f9-650">Bu `default` yolu kullanarak, URL yolu `/Products/List` eyleme eşlenir `ProductsController.List` ve `/Blog/Article/17` ile eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-650">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="ca2f9-651">Bu eşleme **yalnızca** denetleyiciye ve eylem adlarına dayalıdır ve ad alanları, kaynak dosya konumları veya yöntem parametrelerine göre değildir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-651">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="ca2f9-652">Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, tanımladığınız her eylem için yeni bir URL düzeniyle karşılaşmanıza gerek kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-652">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="ca2f9-653">CRUD stilinde eylemlere sahip bir uygulama için denetleyicilerinizdeki URL 'Lerin tutarlılığı, kodunuzun basitleştirilmesine ve Kullanıcı arabiriminizi daha öngörülebilir hale getirmenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-653">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ca2f9-654">, `id` Yol şablonu tarafından isteğe bağlı olarak tanımlanır ve bu, eylemlerinizin URL 'nin bir parçası olarak sağlanmadan yürütebileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-654">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="ca2f9-655">Genellikle `id` URL 'den atlanırsa ne olacağı, `0` model bağlama tarafından ayarlanabileceği ve sonuç olarak veritabanı eşleştirmesinin hiçbir varlık bulunamadığı durumlarda gerçekleşir `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-655">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="ca2f9-656">Öznitelik yönlendirme, bazı eylemler için gereken KIMLIĞI, diğerleri için değil, daha ayrıntılı bir denetim sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-656">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ca2f9-657">Kurala göre belgeler, `id` doğru kullanımda görünebilecekleri gibi isteğe bağlı parametreleri de içerecektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-657">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="ca2f9-658">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="ca2f9-658">Multiple routes</span></span>

<span data-ttu-id="ca2f9-659">`UseMvc`Uygulamasına daha fazla çağrı ekleyerek içine birden çok yol ekleyebilirsiniz `MapRoute` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-659">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="ca2f9-660">Bunun yapılması, birden çok kural tanımlamanızı veya belirli bir eyleme adanmış geleneksel yollar eklemenizi sağlar; örneğin:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-660">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca2f9-661">`blog`Burada yol, geleneksel bir *geleneksel yol* olduğundan geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme ayrılmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-661">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="ca2f9-662">`controller` `action` Yol şablonunda parametre olarak görünmeyin, ancak bu yol yalnızca varsayılan değerlere sahip olabilir ve bu nedenle bu yol her zaman eyleme eşlenir `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-662">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ca2f9-663">Rota koleksiyonundaki yollar sıralanır ve eklendikleri sırada işlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-663">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="ca2f9-664">Bu nedenle bu örnekte yol, `blog` yol öncesinde denenmeye sunulacaktır `default` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-664">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-665">*Adanmış geleneksel yollar* genellıkle, URL yolunun kalan kısmını yakalamak için gibi **catch-all** yol parametrelerini kullanır `{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-665">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="ca2f9-666">Bu, ' çok Greedy ' yolunu diğer yollarla eşleştirirken hedeflediğiniz URL 'Lerle eşleşen bir yol haline getirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-666">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ca2f9-667">Bunu çözümlemek için ' Greedy ' yollarını daha sonra yol tablosuna koyun.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-667">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="ca2f9-668">Geri dönüş</span><span class="sxs-lookup"><span data-stu-id="ca2f9-668">Fallback</span></span>

<span data-ttu-id="ca2f9-669">İstek işlemenin bir parçası olarak, MVC, uygulamanızdaki bir denetleyiciyi ve eylemi bulmak için yol değerlerinin kullanılabileceğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-669">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="ca2f9-670">Rota değerleri bir eylemle eşleşmezse, yol eşleşme olarak kabul edilmez ve sonraki rota denenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-670">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="ca2f9-671">Buna *geri dönüş* denir ve geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-671">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="ca2f9-672">Kesinleştirme eylemleri</span><span class="sxs-lookup"><span data-stu-id="ca2f9-672">Disambiguating actions</span></span>

<span data-ttu-id="ca2f9-673">İki eylem yönlendirme aracılığıyla eşleşiyorsa, MVC ' en iyi ' adayı seçmek için bir özel durum oluşturması veya bir özel durum oluşturmak için, MVC 'nin belirsizliğini</span><span class="sxs-lookup"><span data-stu-id="ca2f9-673">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="ca2f9-674">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-674">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="ca2f9-675">Bu denetleyici, URL yolu ve rota verileri ile eşleşen iki eylemi tanımlar `/Products/Edit/17` `{ controller = Products, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-675">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="ca2f9-676">Bu, `Edit(int)` bir ürünü düzenlemek için bir form gösteren ve `Edit(int, Product)` postalanan formu işleyen MVC denetleyicileri için tipik bir modeldir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-676">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="ca2f9-677">Bunu yapmak için `Edit(int, Product)` , istek BIR http olduğunda `POST` ve `Edit(int)` http fiili başka bir şey olduğunda bu olası MVC 'nin seçim yapması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-677">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="ca2f9-678">`HttpPostAttribute`( `[HttpPost]` ), `IActionConstraint` Yalnızca http fiili olduğunda eylemin seçili olmasını sağlayan bir uygulamasıdır `POST` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-678">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ca2f9-679">' Nin varlığı `IActionConstraint` `Edit(int, Product)` ' daha iyi ' bir eşleşme yapar `Edit(int)` , `Edit(int, Product)` Bu nedenle ilk olarak denenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-679">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="ca2f9-680">Yalnızca özelleştirilmiş senaryolarda özel uygulamalar yazmanız gerekir `IActionConstraint` , ancak benzer öznitelikler gibi özniteliklerin rolün `HttpPostAttribute`  diğer HTTP fiilleri için tanımlandığını anlamak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-680">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="ca2f9-681">Geleneksel yönlendirmesinde, bir iş akışının parçası olduklarında aynı eylem adını kullanmak yaygın olarak karşılaşılan bir işlemdir `show form -> submit form` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-681">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="ca2f9-682">Bu düzenin rahatlığı, [ıactionconstraint 'ı anlama](#understanding-iactionconstraint) bölümünde daha sonra görünür hale gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-682">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="ca2f9-683">Birden çok yol eşleşirse ve MVC ' en iyi ' yolu bulamazsa, bir oluşturur `AmbiguousActionException` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-683">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="ca2f9-684">Yol adları</span><span class="sxs-lookup"><span data-stu-id="ca2f9-684">Route names</span></span>

<span data-ttu-id="ca2f9-685">Dizeler  `"blog"` ve `"default"` Aşağıdaki örneklerde yol adları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-685">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca2f9-686">Yol adları, URL oluşturma için adlandırılmış yolun kullanılabilmesi için yola mantıksal bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-686">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="ca2f9-687">Bu, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı büyük ölçüde basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-687">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ca2f9-688">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-688">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ca2f9-689">Yol adları, isteklerin URL 'SI ile eşleşmesini veya işlenmesini etkilemez; Bunlar yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-689">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="ca2f9-690">[Yönlendirme](xref:fundamentals/routing) , MVC 'ye özgü yardımcılardaki URL oluşturma da dahil olmak üzere URL oluşturma hakkında daha ayrıntılı bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-690">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="ca2f9-691">Öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-691">Attribute routing</span></span>

<span data-ttu-id="ca2f9-692">Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-692">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ca2f9-693">Aşağıdaki örnekte, `app.UseMvc();` `Configure` yönteminde kullanılır ve hiçbir yol geçirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-693">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="ca2f9-694">`HomeController`Varsayılan yolun eşleşeceğini benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-694">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="ca2f9-695">`HomeController.Index()`Bu eylem, veya URL yollarının herhangi biri için yürütülür `/` `/Home` `/Home/Index` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-695">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-696">Bu örnek, öznitelik yönlendirme ve geleneksel yönlendirme arasında bir temel programlama farkı vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-696">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="ca2f9-697">Öznitelik yönlendirme, bir yol belirtmek için daha fazla giriş gerektirir; geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-697">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ca2f9-698">Ancak, öznitelik yönlendirme (ve gerektirir) her eylem için hangi rota şablonlarının uygulanacağını kesin olarak denetler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-698">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="ca2f9-699">Öznitelik yönlendirme ile, denetleyici adı ve eylem adları, **herhangi** bir eylem seçildiği hiçbir rol oynar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-699">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="ca2f9-700">Bu örnek, önceki örnekle aynı URL 'Lerle eşleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-700">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="ca2f9-701">Yukarıdaki yol şablonları,, ve için yol parametreleri `action` tanımlamaz `area` `controller` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-701">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="ca2f9-702">Aslında, öznitelik rotalarında bu yol parametrelerine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-702">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="ca2f9-703">Yol şablonu bir eylemle zaten ilişkili olduğundan, URL 'den eylem adını ayrıştırmak mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-703">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="ca2f9-704">Http [fiil] öznitelikleriyle öznitelik yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-704">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="ca2f9-705">Öznitelik yönlendirme, gibi öznitelikleri de kullanabilir `Http[Verb]` `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-705">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="ca2f9-706">Bu özniteliklerin hepsi bir yol şablonunu kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-706">All of these attributes can accept a route template.</span></span> <span data-ttu-id="ca2f9-707">Bu örnekte, aynı rota şablonuyla eşleşen iki eylem gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-707">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="ca2f9-708">Bir URL yolu için, `/products` `ProductsApi.ListProducts` http fiili olduğunda `GET` ve `ProductsApi.CreateProduct` http fiili olduğunda yürütülecektir, eylem `POST` gibi bir URL yolu yürütülür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-708">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ca2f9-709">Öznitelik yönlendirme öncelikle URL ile yol öznitelikleri tarafından tanımlanan yol şablonları kümesine göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-709">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="ca2f9-710">Bir rota şablonu eşleştiğinde, `IActionConstraint` hangi eylemlerin yürütüleceğini belirleyen kısıtlamalar uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-710">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="ca2f9-711">Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul edecek şekilde bir eylem yönteminde kullanmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-711">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="ca2f9-712">`Http*Verb*Attributes`API 'nizin neleri desteklediği hakkında kesin olması için daha belirgin bir şekilde kullanılması daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-712">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="ca2f9-713">REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-713">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ca2f9-714">Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-714">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ca2f9-715">Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-715">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ca2f9-716">Eylem, gibi bir URL yolu için `ProductsApi.GetProduct(int)` değil, gibi BIR URL yolu için yürütülür `/products/3` `/products` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-716">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="ca2f9-717">Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-717">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="ca2f9-718">Yol adı</span><span class="sxs-lookup"><span data-stu-id="ca2f9-718">Route Name</span></span>

<span data-ttu-id="ca2f9-719">Aşağıdaki kod, bir *yol adı* tanımlar `Products_List` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-719">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ca2f9-720">Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-720">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ca2f9-721">Rota adlarının, yönlendirmenin URL eşleştirme davranışına etkisi yoktur ve yalnızca URL oluşturma için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-721">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="ca2f9-722">Yol adları, uygulama genelinde benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-722">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-723">Bunu, isteğe bağlı () parametresini tanımlayan geleneksel *varsayılan yol* ile kontrast `id` `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-723">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ca2f9-724">API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-724">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="ca2f9-725">Yolları birleştirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-725">Combining routes</span></span>

<span data-ttu-id="ca2f9-726">Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-726">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ca2f9-727">Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-727">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ca2f9-728">Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-728">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="ca2f9-729">Bu örnekte, URL yolu `/products` eşleştirebilir `ProductsApi.ListProducts` ve URL yolu `/products/5` eşleştirebilir `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-729">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="ca2f9-730">Bu eylemlerin her ikisi de `GET` ile işaretlendikleri IÇIN http ile eşleşir `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-730">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="ca2f9-731">Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-731">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ca2f9-732">Bu örnek, *varsayılan rotaya* benzer bir URL yolları kümesiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-732">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="ca2f9-733">Öznitelik yollarını sıralama</span><span class="sxs-lookup"><span data-stu-id="ca2f9-733">Ordering attribute routes</span></span>

<span data-ttu-id="ca2f9-734">Tanımlı bir düzende yürütülen geleneksel yolların aksine, öznitelik yönlendirme bir ağaç oluşturur ve tüm yollarla aynı anda eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-734">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="ca2f9-735">Bu, yol girişleri ideal bir sıralamaya yerleştirildiyse olduğu gibi davranır; en özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-735">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ca2f9-736">Örneğin, gibi bir yol `blog/search/{topic}` gibi bir yol daha özeldir `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-736">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="ca2f9-737">`blog/search/{topic}`Tek yapmanız gereken tek bir sıralama olduğundan, varsayılan olarak ' çalıştırmaları ' yolunu mantıksal olarak konuşuyor.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-737">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="ca2f9-738">Geleneksel yönlendirmeyi kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-738">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ca2f9-739">Öznitelik rotaları, `Order` Tüm Framework yol özniteliklerinin özelliğini kullanarak bir sıra yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-739">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="ca2f9-740">Yollar, özelliğin artan sıralamasına göre işlenir `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-740">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ca2f9-741">Varsayılan sıra `0` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-741">The default order is `0`.</span></span> <span data-ttu-id="ca2f9-742">Kullanarak bir yolun ayarlanması `Order = -1` , bir sipariş ayarlamamadan önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-742">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="ca2f9-743">Kullanarak bir yolun ayarlanması `Order = 1` , varsayılan yol sıralaması sonrasında çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-743">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="ca2f9-744">Uygulamasına bağlı kaçının `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-744">Avoid depending on `Order`.</span></span> <span data-ttu-id="ca2f9-745">URL alanınız, doğru sıralama değerlerinin doğru şekilde yönlendirilmesini gerektiriyorsa, istemciler de kafa karıştırıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-745">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ca2f9-746">Genel öznitelik yönlendirme ' de, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-746">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="ca2f9-747">URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, bir geçersiz kılma olarak yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-747">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ca2f9-748">Razor Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-748">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ca2f9-749">Sayfalar için yol Razor [ Razor ve uygulama kuralları: yol sırası](xref:razor-pages/razor-pages-conventions#route-order)bölümünde yer alan bilgi başlıkları hakkında bilgiler bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-749">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ca2f9-750">Yol şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])</span><span class="sxs-lookup"><span data-stu-id="ca2f9-750">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="ca2f9-751">Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (,) içine alarak *belirteç değiştirmeyi* destekler `[` `]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-751">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="ca2f9-752">, Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-752">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="ca2f9-753">Aşağıdaki örnekte, Eylemler, açıklamalarda açıklandığı gibi URL yollarıyla eşleşir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-753">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ca2f9-754">Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-754">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ca2f9-755">Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-755">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ca2f9-756">Öznitelik rotaları de devralma ile birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-756">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ca2f9-757">Bu özellikle, belirteç değiştirme ile güçlü bir şekilde birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-757">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="ca2f9-758">Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-758">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="ca2f9-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` Her eylem için benzersiz bir yol adı üretir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="ca2f9-760">Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya  `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-760">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ca2f9-761">Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="ca2f9-761">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ca2f9-762">Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-762">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ca2f9-763">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-763">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ca2f9-764">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-764">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ca2f9-765">, `RouteTokenTransformerConvention` Şu şekilde bir uygulama modeli kuralıdır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-765">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="ca2f9-766">Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-766">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ca2f9-767">Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-767">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="ca2f9-768">, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-768">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="ca2f9-769">Birden çok yol</span><span class="sxs-lookup"><span data-stu-id="ca2f9-769">Multiple Routes</span></span>

<span data-ttu-id="ca2f9-770">Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-770">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ca2f9-771">Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi *varsayılan geleneksel yolun* davranışını taklit etmek olur:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-771">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="ca2f9-772">Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biriyle birleşmesi anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-772">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="ca2f9-773">Birden çok yol özniteliği (uygulayan `IActionConstraint` ) bir eyleme yerleştirildiğinde, her eylem kısıtlaması, onu tanımlayan öznitelikten yol şablonuyla birleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-773">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="ca2f9-774">Eylemlerde birden çok yolun kullanılması güçlü görünse de, uygulamanızın URL alanının basit ve iyi tanımlanmış tutulması daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-774">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="ca2f9-775">Yalnızca gerektiğinde eylemler üzerinde birden çok yol kullanın, örneğin mevcut istemcileri desteklemek için.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-775">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ca2f9-776">Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-776">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ca2f9-777">Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-777">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="ca2f9-778">Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-778">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ca2f9-779">Kullanarak özel yol öznitelikleri `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-779">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="ca2f9-780">Çerçevede ( `[Route(...)]` ,, vb.) sunulan tüm rota öznitelikleri, `[HttpGet(...)]` `IRouteTemplateProvider` arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-780">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="ca2f9-781">MVC, uygulama başlatıldığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar ve `IRouteTemplateProvider` ilk rota kümesini oluşturmak için uygulamalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-781">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ca2f9-782">`IRouteTemplateProvider`Kendi yol öznitelerinizi tanımlamak için öğesini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-782">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="ca2f9-783">Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-783">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="ca2f9-784">Yukarıdaki örnekteki özniteliği, `Template` `"api/[controller]"` ne zaman uygulanacağını otomatik olarak olarak ayarlar `[MyApiController]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-784">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ca2f9-785">Öznitelik yollarını özelleştirmek için uygulama modelini kullanma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-785">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="ca2f9-786">*Uygulama modeli* , MVC tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta veriler ile başlangıçta oluşturulan bir nesne modelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-786">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="ca2f9-787">*Uygulama modeli* , yol özniteliklerinden (aracılığıyla) toplanan tüm verileri içerir `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-787">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="ca2f9-788">Yönlendirme işleminin nasıl davranacağını özelleştirmek için, Başlangıç zamanında uygulama modelini değiştirmek üzere *kurallar* yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-788">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="ca2f9-789">Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin basit bir örneği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-789">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ca2f9-790">Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ca2f9-790">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ca2f9-791">MVC uygulamaları, geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-791">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ca2f9-792">Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-792">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ca2f9-793">Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-793">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca2f9-794">Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-794">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca2f9-795">Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-795">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ca2f9-796">Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemlerin yönlendirilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-796">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-797">İki tür yönlendirme sisteminin ayırt edilmesini ne kadar ayırt eden, bir URL bir yol şablonuyla eşleştirdikten sonra uygulanan işlemdir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-797">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="ca2f9-798">Geleneksel yönlendirmesinde, eşleşmeden yol değerleri, tüm geleneksel yönlendirilmiş eylemlerin arama tablosundan eylemi ve denetleyiciyi seçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-798">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="ca2f9-799">Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilendirilir ve başka bir arama gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-799">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="ca2f9-800">Karmaşık segmentler</span><span class="sxs-lookup"><span data-stu-id="ca2f9-800">Complex segments</span></span>

<span data-ttu-id="ca2f9-801">Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]` ), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-801">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="ca2f9-802">Bir açıklama için bkz. [kaynak kodu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-802">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="ca2f9-803">Daha fazla bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-803">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="ca2f9-804">URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-804">URL Generation</span></span>

<span data-ttu-id="ca2f9-805">MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-805">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ca2f9-806">URL oluşturma, kodlarınızın daha sağlam ve sürdürülebilir hale getirilmesi için sorunsuz kodlama URL 'Lerini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-806">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="ca2f9-807">Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-807">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="ca2f9-808">URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-808">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ca2f9-809">`IUrlHelper`Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki temel altyapı parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-809">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ca2f9-810">`IUrlHelper` `Url` Denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir bir örnek bulacaksınız.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-810">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ca2f9-811">Bu örnekte `IUrlHelper` arabirim, `Controller.Url` başka bir eyleme YÖNELIK bir URL oluşturmak için özelliği aracılığıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-811">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-812">Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkenin DEĞERI URL yol dizesi olacaktır `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-812">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ca2f9-813">Bu URL yolu, yönlendirme değerlerini, geçerli istekten (çevresel değerler), `Url.Action` Bu değerleri geçirilen değerlerle ve bu değerlerin yol şablonuna geçirerek birleştirerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-813">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ca2f9-814">Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-814">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ca2f9-815">Bir değere sahip olmayan bir rota parametresi, varsa varsayılan bir değer kullanabilir veya isteğe bağlı ise ( `id` Bu örnekte olduğu gibi) atlanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-815">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="ca2f9-816">Gerekli yol parametresinin karşılık gelen bir değeri yoksa, URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-816">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ca2f9-817">Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-817">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ca2f9-818">`Url.Action`Yukarıdaki örneği geleneksel yönlendirmeyi varsayar, ancak URL oluşturma özniteliği farklı olsa da, öznitelik yönlendirme ile benzer şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-818">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="ca2f9-819">Geleneksel yönlendirme ile, bir şablonu genişletmek için yol değerleri kullanılır ve `controller` `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme Ile eşleşen URL 'ler bir *kurala* bağlı olduğundan, bu işe yarar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-819">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="ca2f9-820">Öznitelik yönlendirmesinde, ve için rota değerlerinin `controller` `action` şablonda görünmesine izin verilmez; bunun yerine kullanılacak şablonu aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-820">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="ca2f9-821">Bu örnek öznitelik yönlendirme kullanır:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-821">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="ca2f9-822">MVC, tüm öznitelik yönlendirilmiş eylemlerinin bir arama tablosunu oluşturur ve `controller` `action` URL oluşturma için kullanılacak yol şablonunu seçmek için ve değerleriyle eşleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-822">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="ca2f9-823">Yukarıdaki örnekte   `custom/url/to/destination` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-823">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ca2f9-824">Eylem adına göre URL 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-824">Generating URLs by action name</span></span>

<span data-ttu-id="ca2f9-825">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-825">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="ca2f9-826">`Action`) ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirterek ne bağlandığınızı belirtmek istediğiniz fikri temel alır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-826">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-827">Kullanırken `Url.Action` , için geçerli yol değerleri `controller` ve `action` değeri için belirtilir `controller` ve `action` her iki *çevresel değerin* **ve** *değerin* bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-827">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="ca2f9-828">Yöntemi `Url.Action` her zaman ve ' nin geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-828">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="ca2f9-829">Yönlendirme, bir URL oluştururken sağlamadığınız bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmayı dener.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-829">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="ca2f9-830">`{a}/{b}/{c}/{d}`Yönlendirme ve ortam değerleri gibi bir yol kullanarak `{ a = Alice, b = Bob, c = Carol, d = David }` , yönlendirmenin tüm rota parametreleri bir değere sahip olduğundan, ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-830">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="ca2f9-831">Değeri eklediyseniz `{ d = Donovan }` değer `{ d = David }` yok sayılır ve oluşturulan URL yolu da olur `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-831">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="ca2f9-832">URL yolları hiyerarşiktir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-832">URL paths are hierarchical.</span></span> <span data-ttu-id="ca2f9-833">Yukarıdaki örnekte, değerini eklediyseniz, `{ c = Cheryl }` her iki değer de `{ c = Carol, d = David }` yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-833">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="ca2f9-834">Bu durumda artık için bir değer yoktur `d` ve URL oluşturma başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-834">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="ca2f9-835">İstenen değerini `c` ve ' i belirtmeniz gerekir `d` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-835">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="ca2f9-836">Varsayılan yol () ile bu soruna yaklaşmanız gerekebilir, `{controller}/{action}/{id?}` ancak `Url.Action` her zaman açıkça bir ve değeri belirtmek için uygulamada bu davranış hakkında nadiren karşılaşacaksınız `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-836">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="ca2f9-837">Daha uzun aşırı yüklemeleri `Url.Action` Ayrıca, ve dışındaki rota parametreleri için değerler sağlamak üzere ek bir *yol değerleri* nesnesi de alır `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-837">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ca2f9-838">Bunun en yaygın olarak bu şekilde kullanıldığını görürsünüz `id` `Url.Action("Buy", "Products", new { id = 17 })` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-838">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ca2f9-839">Kurala göre *yol değerleri* nesnesi genellikle anonim türdeki bir nesnedir, ancak bir `IDictionary<>` veya *düz olarak eski .net nesnesi* de olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-839">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="ca2f9-840">Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-840">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="ca2f9-841">Mutlak URL oluşturmak için şunu kabul eden bir aşırı yükleme kullanın `protocol` : `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-841">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="ca2f9-842">Rotaya göre URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-842">Generating URLs by route</span></span>

<span data-ttu-id="ca2f9-843">Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-843">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ca2f9-844">`IUrlHelper` Ayrıca `Url.RouteUrl` Yöntem ailesini de sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-844">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="ca2f9-845">Bu yöntemler öğesine benzerdir `Url.Action` , ancak geçerli değerlerini `action` ve yol değerlerini kopyalamalardır `controller` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-845">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ca2f9-846">En yaygın kullanım, genellikle bir denetleyici veya eylem *adı belirtmeden,* URL oluşturmak için belirli bir yolu kullanmak üzere bir yol adı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-846">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="ca2f9-847">HTML 'de URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-847">Generating URLs in HTML</span></span>

<span data-ttu-id="ca2f9-848">`IHtmlHelper` , ve `HtmlHelper` `Html.BeginForm` `Html.ActionLink` öğelerini sırasıyla oluşturmak ve oluşturmak için yöntemler sağlar `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-848">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ca2f9-849">Bu yöntemler `Url.Action` BIR URL oluşturmak için yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-849">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ca2f9-850">`Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-850">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ca2f9-851">Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-851">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ca2f9-852">Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-852">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ca2f9-853">Daha fazla bilgi için bkz. [formlarla çalışma](../views/working-with-forms.md) .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-853">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="ca2f9-854">Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-854">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="ca2f9-855">Eylem sonuçlarında URL oluşturma</span><span class="sxs-lookup"><span data-stu-id="ca2f9-855">Generating URLS in Action Results</span></span>

<span data-ttu-id="ca2f9-856">Yukarıdaki örnekler `IUrlHelper` bir denetleyicide kullanılarak gösteriliyor, ancak denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak BIR URL oluşturmak olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-856">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ca2f9-857">`ControllerBase`Ve `Controller` temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-857">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ca2f9-858">Tipik bir kullanım, Kullanıcı girişi kabul edildikten sonra yeniden yönlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-858">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="ca2f9-859">Eylem sonuçları Fabrika yöntemleri, üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-859">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ca2f9-860">Adanmış geleneksel yollar için özel durum</span><span class="sxs-lookup"><span data-stu-id="ca2f9-860">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ca2f9-861">Geleneksel yönlendirme, *adanmış geleneksel yol* olarak adlandırılan özel bir yol tanımı türünü kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-861">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="ca2f9-862">Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-862">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca2f9-863">Bu yol tanımlarının kullanılması, `Url.Action("Index", "Home")` yol Ile URL yolunu oluşturacak `/` `default` , ancak neden?</span><span class="sxs-lookup"><span data-stu-id="ca2f9-863">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="ca2f9-864">Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-864">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ca2f9-865">Adanmış geleneksel yollar, URL oluşturmayla "çok Greedy" olmasını önleyen karşılık gelen bir yol parametresi olmayan varsayılan değerlerin özel bir davranışına bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-865">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="ca2f9-866">Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-866">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ca2f9-867">Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-867">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ca2f9-868">`blog`Değerler eşleşmediğinden, KULLANıLARAK URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-868">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ca2f9-869">Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-869">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ca2f9-870">Alanlar</span><span class="sxs-lookup"><span data-stu-id="ca2f9-870">Areas</span></span>

<span data-ttu-id="ca2f9-871">[Bölgeler](areas.md) , ilgili işlevselliği ayrı bir yönlendirme-ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir grupla düzenlemek için kullanılan bir MVC özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-871">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="ca2f9-872">Alanların kullanılması, bir uygulamanın farklı *alanlara* sahip oldukları sürece aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-872">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="ca2f9-873">Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-873">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ca2f9-874">Bu bölüm, yönlendirmenin alanlarla nasıl etkileşime gireceğini tartışır. alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](areas.md) bakın.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-874">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="ca2f9-875">Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve adlı bir alan için bir *alan yolu* kullanacak şekilde yapılandırır `Blog` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-875">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ca2f9-876">Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` , ilk yol yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-876">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ca2f9-877">`area`Yol değeri, `area` tarafından oluşturulan yolun aşağıdaki değere eşit olduğu aslında için varsayılan bir değer tarafından üretilir `MapAreaRoute` :</span><span class="sxs-lookup"><span data-stu-id="ca2f9-877">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="ca2f9-878">`MapAreaRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-878">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ca2f9-879">Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-879">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="ca2f9-880">Geleneksel yönlendirme sıra bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-880">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca2f9-881">Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-881">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ca2f9-882">Yukarıdaki örneği kullanarak, yol değerleri aşağıdaki eylemle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="ca2f9-882">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ca2f9-883">, Bir `AreaAttribute` alanın parçası olarak denetleyiciyi belirtir, bu denetleyicinin alanında olduğunu varsayalım `Blog` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-883">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="ca2f9-884">Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir  ve `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-884">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ca2f9-885">Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-885">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="ca2f9-886">Her denetleyicinin ad alanı, tamamlanma için burada gösterilir. Aksi takdirde, denetleyicilerde adlandırma çakışması olur ve derleyici hatası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-886">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="ca2f9-887">Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-887">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ca2f9-888">İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-888">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ca2f9-889">Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-889">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-890">Değer *olmadan* eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-890">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ca2f9-891">Bir alan içinde bir eylem yürütürken, için rota değeri, `area` URL oluşturma için kullanılmak üzere yönlendirme için bir *ortam değeri* olarak kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-891">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="ca2f9-892">Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-892">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="ca2f9-893">Iactionconstraint 'i anlama</span><span class="sxs-lookup"><span data-stu-id="ca2f9-893">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="ca2f9-894">Bu bölüm, Framework iç işlevleri hakkında ayrıntılı bir bakış ve MVC 'nin yürütülecek eylemi nasıl seçtiği.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-894">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="ca2f9-895">Tipik bir uygulamanın özel olması gerekmez `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="ca2f9-895">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="ca2f9-896">Arabirime tanıdık olmasanız bile büyük olasılıkla zaten kullandık `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-896">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="ca2f9-897">`[HttpGet]`Özniteliği ve benzer `[Http-VERB]` öznitelikleri, `IActionConstraint` bir eylem yönteminin yürütülmesini sınırlandırmak için uygular.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-897">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="ca2f9-898">Varsayılan geleneksel yol varsayılıyor, URL yolu `/Products/Edit` `{ controller = Products, action = Edit }` burada gösterilen eylemlerin **her ikisiyle de** eşleşen değerleri üretir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-898">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="ca2f9-899">`IActionConstraint`Terminolojisinde, her ikisi de rota verileriyle eşleşen her iki eylemin da aday kabul edileceğini söyliyoruz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-899">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="ca2f9-900">`HttpGetAttribute`Yürütüldüğünde, *Düzenle ()* , *Get* için BIR eşleşmedir ve diğer http fiili için bir eşleşme değildir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-900">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="ca2f9-901">`Edit(...)`Eylemin tanımlı hiçbir kısıtlaması yok, bu nedenle herhangi BIR http fiili ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-901">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="ca2f9-902">Bu nedenle, yalnızca bir eşleşme olduğunu varsayıyoruz `POST` `Edit(...)` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-902">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="ca2f9-903">Ancak, `GET` her iki eylem de eşleşemez, ancak bir eylem, olmadan bir eylemden `IActionConstraint` her zaman *daha iyi* kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-903">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="ca2f9-904">Bu nedenle `Edit()` `[HttpGet]` , daha belirli bir kabul edildiğinden ve her iki eylem de eşleşeceğinden seçilecek.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-904">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="ca2f9-905">Kavramsal olarak, `IActionConstraint` bir *aşırı yükleme* biçimidir, ancak aynı ada sahip yöntemlerin aşırı YÜKLENMESI yerine aynı URL ile eşleşen eylemler arasında aşırı yükleme yapılır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-905">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="ca2f9-906">Öznitelik yönlendirme Ayrıca, `IActionConstraint` farklı denetleyicilerle her ikisi de olarak kabul edilen eylemlere neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-906">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="ca2f9-907">Iactionconstraint uygulama</span><span class="sxs-lookup"><span data-stu-id="ca2f9-907">Implementing IActionConstraint</span></span>

<span data-ttu-id="ca2f9-908">' Nin uygulanmasının en kolay yolu, `IActionConstraint` ' dan türetilmiş bir sınıf oluşturmaktır `System.Attribute` ve bunları eylemleriniz ve denetleyicilerinize yerleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-908">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="ca2f9-909">MVC, öznitelik olarak uygulanan her türlü otomatik olarak keşfedilir `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-909">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="ca2f9-910">Kısıtlama uygulamak için uygulama modelini kullanabilirsiniz ve bu, büyük olasılıkla en esnek yaklaşımdır ve bu sayede, nasıl uygulanabileceğini meta programlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-910">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="ca2f9-911">Aşağıdaki örnekte, bir kısıtlama yol verilerinden bir *ülke kodunu* temel alan bir eylem seçer.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-911">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="ca2f9-912">[GitHub 'daki tam örnek](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="ca2f9-912">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="ca2f9-913">Yöntemi uygulamaktan `Accept` ve kısıtlamanın yürütülmesi için bir ' Order ' seçmeye sorumlusunuz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-913">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="ca2f9-914">Bu durumda, yöntemi, `Accept` `true` yol değeri eşleştiğinde eylemin bir eşleşme olduğunu belirtmek için öğesini döndürür `country` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-914">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="ca2f9-915">Bu, öğesinden farklı bir `RouteValueAttribute` eyleme geri dönüş yapılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-915">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="ca2f9-916">Örnek, bir eylem tanımlarsanız, `en-US` gibi bir ülke kodunun `fr-FR` , uygulanmamış daha fazla genel denetleyiciye geri dönemeyeceğini gösterir `[CountrySpecific(...)]` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-916">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="ca2f9-917">`Order`Özelliği kısıtlamanın parçası olan *aşamayı* belirler.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-917">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="ca2f9-918">Eylem kısıtlamaları, gruplarına göre gruplar içinde çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca2f9-918">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="ca2f9-919">Örneğin, tüm Framework tarafından sunulan HTTP yöntemi öznitelikleri aynı `Order` aşamada çalışacak şekilde aynı değeri kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-919">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="ca2f9-920">İstediğiniz ilkeleri uygulamak için ihtiyacınız olan çok sayıda aşamaya sahip olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-920">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="ca2f9-921">Bir değere karar vermek için, `Order` kısıtlamalarınızın http yöntemlerinden önce uygulanıp uygulanmayacağı hakkında düşünün.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-921">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="ca2f9-922">Daha az sayı önce çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca2f9-922">Lower numbers run first.</span></span>

::: moniker-end
