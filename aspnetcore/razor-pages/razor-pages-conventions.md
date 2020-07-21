---
title: RazorASP.NET Core 'de sayfa yönlendirme ve uygulama kuralları
author: rick-anderson
description: Yönlendirme ve uygulama modeli sağlayıcısı kurallarının sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemenize nasıl yardımcı olduğunu öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 3cb83d8cfd058c4d0a93ece9a4f19b6407dac384
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568866"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="835fc-103">ASP.NET Core 'de sayfa yönlendirme ve uygulama kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-103"> Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="835fc-104">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="835fc-105">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="835fc-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="835fc-106">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="835fc-107">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="835fc-108">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="835fc-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="835fc-109">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:mvc/controllers/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="835fc-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="835fc-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="835fc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="835fc-111">Senaryo</span><span class="sxs-lookup"><span data-stu-id="835fc-111">Scenario</span></span> | <span data-ttu-id="835fc-112">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="835fc-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="835fc-113">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="835fc-114">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="835fc-114">Conventions.Add</span></span><ul><li><span data-ttu-id="835fc-115">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-116">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-117">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="835fc-118">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="835fc-119">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="835fc-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="835fc-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="835fc-123">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="835fc-124">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="835fc-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-127">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="835fc-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="835fc-128">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="835fc-129">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> içinde yapılandıran bir aşırı yükleme kullanılarak <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> yapılandırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="835fc-129"> Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="835fc-130">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="835fc-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a><span data-ttu-id="835fc-131">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="835fc-131">Route order</span></span>

<span data-ttu-id="835fc-132">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="835fc-133">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="835fc-133">Order</span></span>            | <span data-ttu-id="835fc-134">Davranış</span><span class="sxs-lookup"><span data-stu-id="835fc-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="835fc-135">-1</span><span class="sxs-lookup"><span data-stu-id="835fc-135">-1</span></span>               | <span data-ttu-id="835fc-136">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="835fc-137">0</span><span class="sxs-lookup"><span data-stu-id="835fc-137">0</span></span>                | <span data-ttu-id="835fc-138">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="835fc-138">Order isn't specified (default value).</span></span> <span data-ttu-id="835fc-139">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="835fc-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="835fc-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="835fc-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="835fc-141">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="835fc-142">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="835fc-143">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="835fc-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="835fc-144">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="835fc-145">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="835fc-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="835fc-146">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="835fc-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="835fc-147">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="835fc-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="835fc-148">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="835fc-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="835fc-149">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="835fc-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="835fc-150">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="835fc-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="835fc-151">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="835fc-152">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="835fc-152"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="835fc-153">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="835fc-154">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-154">Model conventions</span></span>

<span data-ttu-id="835fc-155"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="835fc-156">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="835fc-157"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="835fc-158">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="835fc-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-159">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="835fc-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="835fc-160">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="835fc-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="835fc-161">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="835fc-162">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="835fc-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="835fc-163">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-164">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-165">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="835fc-166">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-167">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-168">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-169">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-170">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-170">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="835fc-171">' <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Razor Deki hizmet koleksiyonuna sayfa eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="835fc-171"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when Razor Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="835fc-172">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="835fc-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="835fc-173">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="835fc-176">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="835fc-177"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="835fc-178">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="835fc-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="835fc-179">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="835fc-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="835fc-180">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="835fc-181">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="835fc-182">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="835fc-184">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="835fc-186">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="835fc-187"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="835fc-189">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-189">Page route action conventions</span></span>

<span data-ttu-id="835fc-190"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="835fc-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="835fc-191">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-191">Folder route model convention</span></span>

<span data-ttu-id="835fc-192"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="835fc-193">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="835fc-194">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-195">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-196">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-197">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-198">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-199">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="835fc-202">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-202">Page route model convention</span></span>

<span data-ttu-id="835fc-203"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-204">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="835fc-205">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-206">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-207">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-208">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-209">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-210">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="835fc-213">Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="835fc-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="835fc-214">ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="835fc-215">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="835fc-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="835fc-216">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="835fc-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="835fc-217">`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="835fc-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="835fc-218">Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="835fc-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="835fc-219">`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="835fc-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="835fc-220">Yönergeyle eklenen yol parçalarını dönüştürmez `@page` .</span><span class="sxs-lookup"><span data-stu-id="835fc-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="835fc-221">Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="835fc-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="835fc-222">, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="835fc-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="835fc-223">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="835fc-223">Configure a page route</span></span>

<span data-ttu-id="835fc-224"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="835fc-225">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="835fc-226">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="835fc-227">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="835fc-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="835fc-228">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="835fc-229">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="835fc-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="835fc-230">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="835fc-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="835fc-231">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="835fc-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="835fc-234">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="835fc-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="835fc-235">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="835fc-238">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-238">Page model action conventions</span></span>

<span data-ttu-id="835fc-239">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="835fc-240">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="835fc-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="835fc-241">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="835fc-242">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="835fc-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="835fc-243">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-243">**Folder app model convention**</span></span>

<span data-ttu-id="835fc-244"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="835fc-245">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="835fc-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="835fc-246">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="835fc-248">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-248">**Page app model convention**</span></span>

<span data-ttu-id="835fc-249"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-250">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="835fc-251">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="835fc-253">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-253">**Configure a filter**</span></span>

<span data-ttu-id="835fc-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="835fc-255">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="835fc-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="835fc-256">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="835fc-257">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="835fc-258">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="835fc-259">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="835fc-260">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="835fc-261">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="835fc-263">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-263">**Configure a filter factory**</span></span>

<span data-ttu-id="835fc-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="835fc-265">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="835fc-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="835fc-267">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="835fc-269">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="835fc-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="835fc-270">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="835fc-271">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="835fc-272">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="835fc-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="835fc-273">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="835fc-274">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="835fc-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="835fc-275">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="835fc-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="835fc-276">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="835fc-277">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="835fc-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="835fc-278">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="835fc-279">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="835fc-280">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="835fc-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="835fc-281">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="835fc-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="835fc-282">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="835fc-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="835fc-283">Senaryo</span><span class="sxs-lookup"><span data-stu-id="835fc-283">Scenario</span></span> | <span data-ttu-id="835fc-284">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="835fc-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="835fc-285">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="835fc-286">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="835fc-286">Conventions.Add</span></span><ul><li><span data-ttu-id="835fc-287">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-288">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-289">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="835fc-290">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="835fc-291">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="835fc-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="835fc-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="835fc-295">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="835fc-296">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="835fc-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-299">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="835fc-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="835fc-300">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="835fc-301">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="835fc-301"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="835fc-302">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="835fc-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="835fc-303">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="835fc-303">Route order</span></span>

<span data-ttu-id="835fc-304">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="835fc-305">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="835fc-305">Order</span></span>            | <span data-ttu-id="835fc-306">Davranış</span><span class="sxs-lookup"><span data-stu-id="835fc-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="835fc-307">-1</span><span class="sxs-lookup"><span data-stu-id="835fc-307">-1</span></span>               | <span data-ttu-id="835fc-308">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="835fc-309">0</span><span class="sxs-lookup"><span data-stu-id="835fc-309">0</span></span>                | <span data-ttu-id="835fc-310">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="835fc-310">Order isn't specified (default value).</span></span> <span data-ttu-id="835fc-311">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="835fc-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="835fc-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="835fc-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="835fc-313">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="835fc-314">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="835fc-315">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="835fc-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="835fc-316">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="835fc-317">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="835fc-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="835fc-318">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="835fc-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="835fc-319">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="835fc-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="835fc-320">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="835fc-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="835fc-321">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="835fc-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="835fc-322">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="835fc-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="835fc-323">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="835fc-324">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="835fc-324"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="835fc-325">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="835fc-326">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-326">Model conventions</span></span>

<span data-ttu-id="835fc-327"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="835fc-328">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="835fc-329"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="835fc-330">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="835fc-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-331">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="835fc-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="835fc-332">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="835fc-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="835fc-333">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="835fc-334">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="835fc-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="835fc-335">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-336">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-337">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="835fc-338">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-339">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-340">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-341">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-342">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-342">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="835fc-343"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="835fc-343"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="835fc-344">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="835fc-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="835fc-345">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="835fc-348">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="835fc-349"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="835fc-350">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="835fc-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="835fc-351">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="835fc-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="835fc-352">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="835fc-353">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="835fc-354">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="835fc-356">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="835fc-358">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="835fc-359"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="835fc-361">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-361">Page route action conventions</span></span>

<span data-ttu-id="835fc-362"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="835fc-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="835fc-363">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-363">Folder route model convention</span></span>

<span data-ttu-id="835fc-364"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="835fc-365">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="835fc-366">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-367">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-368">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-369">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-370">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-371">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="835fc-374">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-374">Page route model convention</span></span>

<span data-ttu-id="835fc-375"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-376">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="835fc-377">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-378">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-379">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-380">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-381">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-382">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="835fc-385">Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="835fc-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="835fc-386">ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="835fc-387">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="835fc-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="835fc-388">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="835fc-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="835fc-389">`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="835fc-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="835fc-390">Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="835fc-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="835fc-391">`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="835fc-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="835fc-392">Yönergeyle eklenen yol parçalarını dönüştürmez `@page` .</span><span class="sxs-lookup"><span data-stu-id="835fc-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="835fc-393">Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="835fc-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="835fc-394">, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="835fc-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a><span data-ttu-id="835fc-395">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="835fc-395">Configure a page route</span></span>

<span data-ttu-id="835fc-396"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="835fc-397">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="835fc-398">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="835fc-399">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="835fc-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="835fc-400">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="835fc-401">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="835fc-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="835fc-402">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="835fc-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="835fc-403">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="835fc-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="835fc-406">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="835fc-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="835fc-407">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="835fc-410">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-410">Page model action conventions</span></span>

<span data-ttu-id="835fc-411">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="835fc-412">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="835fc-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="835fc-413">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="835fc-414">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="835fc-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="835fc-415">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-415">**Folder app model convention**</span></span>

<span data-ttu-id="835fc-416"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="835fc-417">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="835fc-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="835fc-418">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="835fc-420">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-420">**Page app model convention**</span></span>

<span data-ttu-id="835fc-421"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-422">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="835fc-423">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="835fc-425">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-425">**Configure a filter**</span></span>

<span data-ttu-id="835fc-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="835fc-427">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="835fc-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="835fc-428">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="835fc-429">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="835fc-430">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="835fc-431">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="835fc-432">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="835fc-433">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="835fc-435">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-435">**Configure a filter factory**</span></span>

<span data-ttu-id="835fc-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="835fc-437">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="835fc-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="835fc-439">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="835fc-441">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="835fc-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="835fc-442">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="835fc-443">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="835fc-444">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="835fc-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="835fc-445">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="835fc-446">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="835fc-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="835fc-447">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="835fc-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="835fc-448">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="835fc-449">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="835fc-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="835fc-450">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="835fc-451">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="835fc-452">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="835fc-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="835fc-453">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="835fc-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="835fc-454">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="835fc-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="835fc-455">Senaryo</span><span class="sxs-lookup"><span data-stu-id="835fc-455">Scenario</span></span> | <span data-ttu-id="835fc-456">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="835fc-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="835fc-457">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="835fc-458">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="835fc-458">Conventions.Add</span></span><ul><li><span data-ttu-id="835fc-459">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-460">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-461">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="835fc-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="835fc-462">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="835fc-463">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="835fc-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="835fc-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="835fc-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="835fc-467">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="835fc-468">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="835fc-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="835fc-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="835fc-471">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="835fc-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="835fc-472">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="835fc-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="835fc-473">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="835fc-473"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="835fc-474">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="835fc-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="835fc-475">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="835fc-475">Route order</span></span>

<span data-ttu-id="835fc-476">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="835fc-477">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="835fc-477">Order</span></span>            | <span data-ttu-id="835fc-478">Davranış</span><span class="sxs-lookup"><span data-stu-id="835fc-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="835fc-479">-1</span><span class="sxs-lookup"><span data-stu-id="835fc-479">-1</span></span>               | <span data-ttu-id="835fc-480">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="835fc-481">0</span><span class="sxs-lookup"><span data-stu-id="835fc-481">0</span></span>                | <span data-ttu-id="835fc-482">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="835fc-482">Order isn't specified (default value).</span></span> <span data-ttu-id="835fc-483">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="835fc-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="835fc-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="835fc-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="835fc-485">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="835fc-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="835fc-486">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="835fc-487">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="835fc-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="835fc-488">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="835fc-489">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="835fc-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="835fc-490">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="835fc-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="835fc-491">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="835fc-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="835fc-492">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="835fc-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="835fc-493">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="835fc-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="835fc-494">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="835fc-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="835fc-495">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="835fc-496">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="835fc-496"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="835fc-497">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="835fc-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="835fc-498">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="835fc-498">Model conventions</span></span>

<span data-ttu-id="835fc-499"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="835fc-500">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="835fc-501"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="835fc-502">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="835fc-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-503">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="835fc-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="835fc-504">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="835fc-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="835fc-505">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="835fc-506">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="835fc-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="835fc-507">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-508">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-509">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="835fc-510">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="835fc-511">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="835fc-512">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-513">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-514">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-514">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="835fc-515"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="835fc-515"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="835fc-516">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="835fc-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="835fc-517">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="835fc-520">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="835fc-521"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="835fc-522">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="835fc-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="835fc-523">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="835fc-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="835fc-524">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="835fc-525">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="835fc-526">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="835fc-528">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="835fc-530">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="835fc-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="835fc-531"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="835fc-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="835fc-533">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-533">Page route action conventions</span></span>

<span data-ttu-id="835fc-534"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="835fc-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="835fc-535">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-535">Folder route model convention</span></span>

<span data-ttu-id="835fc-536"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="835fc-537">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="835fc-538">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-539">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-540">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="835fc-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-541">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-542">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-543">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="835fc-546">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="835fc-546">Page route model convention</span></span>

<span data-ttu-id="835fc-547"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-548">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="835fc-549">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="835fc-550">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="835fc-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="835fc-551">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="835fc-552">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="835fc-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="835fc-553">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="835fc-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="835fc-554">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="835fc-557">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="835fc-557">Configure a page route</span></span>

<span data-ttu-id="835fc-558"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="835fc-559">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="835fc-560">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="835fc-561">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="835fc-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="835fc-562">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="835fc-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="835fc-563">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="835fc-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="835fc-564">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="835fc-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="835fc-565">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="835fc-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="835fc-568">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="835fc-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="835fc-569">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="835fc-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="835fc-572">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="835fc-572">Page model action conventions</span></span>

<span data-ttu-id="835fc-573">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="835fc-574">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="835fc-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="835fc-575">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="835fc-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="835fc-576">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="835fc-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="835fc-577">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-577">**Folder app model convention**</span></span>

<span data-ttu-id="835fc-578"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="835fc-579">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="835fc-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="835fc-580">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="835fc-582">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="835fc-582">**Page app model convention**</span></span>

<span data-ttu-id="835fc-583"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="835fc-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="835fc-584">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="835fc-585">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="835fc-587">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-587">**Configure a filter**</span></span>

<span data-ttu-id="835fc-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="835fc-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="835fc-589">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="835fc-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="835fc-590">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="835fc-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="835fc-591">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="835fc-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="835fc-592">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="835fc-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="835fc-593">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="835fc-594">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="835fc-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="835fc-595">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="835fc-597">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="835fc-597">**Configure a filter factory**</span></span>

<span data-ttu-id="835fc-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="835fc-599">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="835fc-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="835fc-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="835fc-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="835fc-601">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="835fc-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="835fc-603">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="835fc-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="835fc-604">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="835fc-605">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="835fc-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="835fc-606">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="835fc-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="835fc-607">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="835fc-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="835fc-608">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="835fc-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="835fc-609">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="835fc-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
