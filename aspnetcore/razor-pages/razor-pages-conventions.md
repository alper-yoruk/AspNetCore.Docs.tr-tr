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
ms.openlocfilehash: 308ca4401289a55e5dba8d61de50644cb2a53433
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405256"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="9aa10-103">ASP.NET Core 'de sayfa yönlendirme ve uygulama kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-103"> Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9aa10-104">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="9aa10-105">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="9aa10-106">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="9aa10-107">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="9aa10-108">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="9aa10-109">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:mvc/controllers/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="9aa10-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="9aa10-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9aa10-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="9aa10-111">Senaryo</span><span class="sxs-lookup"><span data-stu-id="9aa10-111">Scenario</span></span> | <span data-ttu-id="9aa10-112">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="9aa10-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="9aa10-113">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="9aa10-114">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="9aa10-114">Conventions.Add</span></span><ul><li><span data-ttu-id="9aa10-115">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-116">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-117">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="9aa10-118">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="9aa10-119">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="9aa10-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="9aa10-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="9aa10-123">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="9aa10-124">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="9aa10-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-127">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="9aa10-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="9aa10-128">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="9aa10-129">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-129"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="9aa10-130">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="route-order"></a><span data-ttu-id="9aa10-131">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="9aa10-131">Route order</span></span>

<span data-ttu-id="9aa10-132">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="9aa10-133">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="9aa10-133">Order</span></span>            | <span data-ttu-id="9aa10-134">Davranış</span><span class="sxs-lookup"><span data-stu-id="9aa10-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="9aa10-135">-1</span><span class="sxs-lookup"><span data-stu-id="9aa10-135">-1</span></span>               | <span data-ttu-id="9aa10-136">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="9aa10-137">0</span><span class="sxs-lookup"><span data-stu-id="9aa10-137">0</span></span>                | <span data-ttu-id="9aa10-138">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="9aa10-138">Order isn't specified (default value).</span></span> <span data-ttu-id="9aa10-139">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="9aa10-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="9aa10-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="9aa10-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="9aa10-141">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="9aa10-142">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="9aa10-143">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="9aa10-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="9aa10-144">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="9aa10-145">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="9aa10-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="9aa10-146">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="9aa10-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="9aa10-147">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="9aa10-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="9aa10-148">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="9aa10-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="9aa10-149">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="9aa10-150">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="9aa10-151">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="9aa10-152">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-152"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9aa10-153">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="9aa10-154">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-154">Model conventions</span></span>

<span data-ttu-id="9aa10-155"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-156">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="9aa10-157"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="9aa10-158">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="9aa10-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-159">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="9aa10-160">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="9aa10-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="9aa10-161">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="9aa10-162">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="9aa10-163">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-164">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-165">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="9aa10-166">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-167">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-168">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-169">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-170">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-170">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="9aa10-171"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-171"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9aa10-172">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="9aa10-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="9aa10-173">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-176">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="9aa10-177"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="9aa10-178">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="9aa10-179">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9aa10-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="9aa10-180">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="9aa10-181">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="9aa10-182">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="9aa10-184">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-186">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="9aa10-187"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="9aa10-189">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-189">Page route action conventions</span></span>

<span data-ttu-id="9aa10-190"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="9aa10-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="9aa10-191">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-191">Folder route model convention</span></span>

<span data-ttu-id="9aa10-192"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-193">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="9aa10-194">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-195">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-196">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-197">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-198">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-199">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="9aa10-202">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-202">Page route model convention</span></span>

<span data-ttu-id="9aa10-203"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-204">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="9aa10-205">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-206">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-207">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-208">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-209">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-210">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="9aa10-213">Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="9aa10-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="9aa10-214">ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="9aa10-215">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="9aa10-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="9aa10-216">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="9aa10-217">`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="9aa10-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="9aa10-218">Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="9aa10-219">`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="9aa10-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="9aa10-220">Yönergeyle eklenen yol parçalarını dönüştürmez `@page` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="9aa10-221">Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="9aa10-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="9aa10-222">, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="9aa10-223">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9aa10-223">Configure a page route</span></span>

<span data-ttu-id="9aa10-224"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="9aa10-225">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="9aa10-226">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="9aa10-227">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9aa10-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="9aa10-228">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="9aa10-229">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="9aa10-230">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="9aa10-231">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="9aa10-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="9aa10-234">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="9aa10-235">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="9aa10-238">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-238">Page model action conventions</span></span>

<span data-ttu-id="9aa10-239">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="9aa10-240">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="9aa10-241">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="9aa10-242">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="9aa10-243">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-243">**Folder app model convention**</span></span>

<span data-ttu-id="9aa10-244"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-245">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="9aa10-246">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="9aa10-248">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-248">**Page app model convention**</span></span>

<span data-ttu-id="9aa10-249"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-250">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="9aa10-251">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="9aa10-253">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-253">**Configure a filter**</span></span>

<span data-ttu-id="9aa10-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="9aa10-255">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="9aa10-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="9aa10-256">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="9aa10-257">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="9aa10-258">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="9aa10-259">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="9aa10-260">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="9aa10-261">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="9aa10-263">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-263">**Configure a filter factory**</span></span>

<span data-ttu-id="9aa10-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="9aa10-265">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="9aa10-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="9aa10-267">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="9aa10-269">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="9aa10-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="9aa10-270">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="9aa10-271">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="9aa10-272">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="9aa10-273">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="9aa10-274">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="9aa10-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa10-275">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9aa10-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9aa10-276">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="9aa10-277">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="9aa10-278">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="9aa10-279">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="9aa10-280">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="9aa10-281">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="9aa10-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="9aa10-282">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9aa10-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="9aa10-283">Senaryo</span><span class="sxs-lookup"><span data-stu-id="9aa10-283">Scenario</span></span> | <span data-ttu-id="9aa10-284">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="9aa10-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="9aa10-285">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="9aa10-286">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="9aa10-286">Conventions.Add</span></span><ul><li><span data-ttu-id="9aa10-287">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-288">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-289">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="9aa10-290">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="9aa10-291">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="9aa10-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="9aa10-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="9aa10-295">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="9aa10-296">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="9aa10-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-299">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="9aa10-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="9aa10-300">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="9aa10-301">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-301"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="9aa10-302">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="9aa10-303">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="9aa10-303">Route order</span></span>

<span data-ttu-id="9aa10-304">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="9aa10-305">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="9aa10-305">Order</span></span>            | <span data-ttu-id="9aa10-306">Davranış</span><span class="sxs-lookup"><span data-stu-id="9aa10-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="9aa10-307">-1</span><span class="sxs-lookup"><span data-stu-id="9aa10-307">-1</span></span>               | <span data-ttu-id="9aa10-308">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="9aa10-309">0</span><span class="sxs-lookup"><span data-stu-id="9aa10-309">0</span></span>                | <span data-ttu-id="9aa10-310">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="9aa10-310">Order isn't specified (default value).</span></span> <span data-ttu-id="9aa10-311">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="9aa10-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="9aa10-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="9aa10-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="9aa10-313">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="9aa10-314">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="9aa10-315">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="9aa10-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="9aa10-316">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="9aa10-317">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="9aa10-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="9aa10-318">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="9aa10-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="9aa10-319">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="9aa10-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="9aa10-320">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="9aa10-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="9aa10-321">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="9aa10-322">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="9aa10-323">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="9aa10-324">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-324"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9aa10-325">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="9aa10-326">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-326">Model conventions</span></span>

<span data-ttu-id="9aa10-327"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-328">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="9aa10-329"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="9aa10-330">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="9aa10-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-331">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="9aa10-332">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="9aa10-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="9aa10-333">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="9aa10-334">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="9aa10-335">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-336">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-337">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="9aa10-338">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-339">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-340">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-341">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-342">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-342">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="9aa10-343"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-343"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9aa10-344">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="9aa10-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="9aa10-345">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-348">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="9aa10-349"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="9aa10-350">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="9aa10-351">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9aa10-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="9aa10-352">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="9aa10-353">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="9aa10-354">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="9aa10-356">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-358">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="9aa10-359"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="9aa10-361">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-361">Page route action conventions</span></span>

<span data-ttu-id="9aa10-362"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="9aa10-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="9aa10-363">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-363">Folder route model convention</span></span>

<span data-ttu-id="9aa10-364"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-365">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="9aa10-366">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-367">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-368">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-369">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-370">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-371">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="9aa10-374">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-374">Page route model convention</span></span>

<span data-ttu-id="9aa10-375"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-376">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="9aa10-377">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-378">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-379">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-380">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-381">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-382">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="9aa10-385">Sayfa yollarını özelleştirmek için bir parametre transformatörü kullanın</span><span class="sxs-lookup"><span data-stu-id="9aa10-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="9aa10-386">ASP.NET Core tarafından oluşturulan sayfa yolları, bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="9aa10-387">Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="9aa10-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="9aa10-388">Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="9aa10-389">`PageRouteTransformerConvention`Sayfa yolu modeli kuralı, bir uygulamadaki otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı kesimlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="9aa10-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="9aa10-390">Örneğin, Razor */Pages/subscriptionmanagement/viewAll.exe* konumundaki sayfa dosyası, yolu ' den ' e yeniden yazıldı `/SubscriptionManagement/ViewAll` `/subscription-management/view-all` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="9aa10-391">`PageRouteTransformerConvention`yalnızca Razor sayfalar klasöründen ve dosya adından gelen bir sayfa yolunun otomatik olarak oluşturulan segmentlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="9aa10-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="9aa10-392">Yönergeyle eklenen yol parçalarını dönüştürmez `@page` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="9aa10-393">Kural, tarafından eklenen yolları da dönüştürmez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .</span><span class="sxs-lookup"><span data-stu-id="9aa10-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="9aa10-394">, `PageRouteTransformerConvention` İçinde bir seçenek olarak kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="9aa10-395">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9aa10-395">Configure a page route</span></span>

<span data-ttu-id="9aa10-396"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="9aa10-397">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="9aa10-398">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="9aa10-399">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9aa10-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="9aa10-400">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="9aa10-401">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="9aa10-402">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="9aa10-403">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="9aa10-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="9aa10-406">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="9aa10-407">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="9aa10-410">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-410">Page model action conventions</span></span>

<span data-ttu-id="9aa10-411">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="9aa10-412">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="9aa10-413">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="9aa10-414">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="9aa10-415">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-415">**Folder app model convention**</span></span>

<span data-ttu-id="9aa10-416"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-417">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="9aa10-418">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="9aa10-420">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-420">**Page app model convention**</span></span>

<span data-ttu-id="9aa10-421"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-422">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="9aa10-423">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="9aa10-425">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-425">**Configure a filter**</span></span>

<span data-ttu-id="9aa10-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="9aa10-427">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="9aa10-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="9aa10-428">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="9aa10-429">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="9aa10-430">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="9aa10-431">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="9aa10-432">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="9aa10-433">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="9aa10-435">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-435">**Configure a filter factory**</span></span>

<span data-ttu-id="9aa10-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="9aa10-437">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="9aa10-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="9aa10-439">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="9aa10-441">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="9aa10-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="9aa10-442">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="9aa10-443">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="9aa10-444">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="9aa10-445">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="9aa10-446">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="9aa10-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa10-447">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9aa10-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9aa10-448">Sayfalar uygulamalarında sayfa yönlendirmeyi, bulmayı ve işlemeyi denetlemek için sayfa [yolu ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="9aa10-449">Ayrı sayfalar için özel sayfa yolları yapılandırmanız gerektiğinde, bu konunun ilerleyen kısımlarında açıklanan [Addpageroute kuralına](#configure-a-page-route) sahip sayfalara yönlendirmeyi yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="9aa10-450">Bir sayfa yolu belirtmek, yol kesimleri eklemek veya bir rotaya parametre eklemek için, sayfanın `@page` yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="9aa10-451">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="9aa10-452">Yol kesimleri veya parametre adları olarak kullanılamayan ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="9aa10-453">Daha fazla bilgi için bkz. [Yönlendirme: ayrılmış yönlendirme adları](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="9aa10-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="9aa10-454">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9aa10-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="9aa10-455">Senaryo</span><span class="sxs-lookup"><span data-stu-id="9aa10-455">Scenario</span></span> | <span data-ttu-id="9aa10-456">Örnek gösterilmektedir...</span><span class="sxs-lookup"><span data-stu-id="9aa10-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="9aa10-457">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="9aa10-458">Kurallar. Add</span><span class="sxs-lookup"><span data-stu-id="9aa10-458">Conventions.Add</span></span><ul><li><span data-ttu-id="9aa10-459">Ipageroutemodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-460">Ipageapplicationmodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-461">Ipagehandlermodelconvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="9aa10-462">Uygulamanın sayfalarına bir yol şablonu ve üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="9aa10-463">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="9aa10-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="9aa10-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="9aa10-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="9aa10-467">Bir klasördeki sayfalara ve tek bir sayfaya rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="9aa10-468">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="9aa10-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9aa10-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9aa10-471">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="9aa10-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="9aa10-472">Bir klasördeki sayfalara üst bilgi ekleyin, tek bir sayfaya üst bilgi ekleyin ve bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) yapılandırarak uygulamanın sayfalarına üst bilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

Razor<span data-ttu-id="9aa10-473">Sayfa kuralları, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> sınıfındaki hizmet koleksiyonunda öğesine genişletme yöntemi kullanılarak eklenir ve yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-473"> Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="9aa10-474">Aşağıdaki kural örnekleri bu konunun ilerleyen kısımlarında açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="9aa10-475">Rota sırası</span><span class="sxs-lookup"><span data-stu-id="9aa10-475">Route order</span></span>

<span data-ttu-id="9aa10-476">Rotalar bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> işlem için (rota eşleştirme) belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="9aa10-477">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="9aa10-477">Order</span></span>            | <span data-ttu-id="9aa10-478">Davranış</span><span class="sxs-lookup"><span data-stu-id="9aa10-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="9aa10-479">-1</span><span class="sxs-lookup"><span data-stu-id="9aa10-479">-1</span></span>               | <span data-ttu-id="9aa10-480">Yol, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="9aa10-481">0</span><span class="sxs-lookup"><span data-stu-id="9aa10-481">0</span></span>                | <span data-ttu-id="9aa10-482">Sıra belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="9aa10-482">Order isn't specified (default value).</span></span> <span data-ttu-id="9aa10-483">Atama değil `Order` ( `Order = null` ), `Order` işleme için varsayılan yolu 0 (sıfır) olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="9aa10-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="9aa10-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="9aa10-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="9aa10-485">Yol işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="9aa10-486">Yol işleme, kurala göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="9aa10-487">Yollar sıralı sırada işlenir (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="9aa10-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="9aa10-488">Yollar aynı olduğunda `Order` , en belirli yol önce daha az özel yollarla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="9aa10-489">Aynı `Order` ve aynı parametre sayısına sahip rotalar bir Istek URL 'siyle eşleşiyorsa, rotalar öğesine eklendikleri sırada işlenir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .</span><span class="sxs-lookup"><span data-stu-id="9aa10-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="9aa10-490">Mümkünse, belirlenen bir yol işleme sırasına bağlı olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="9aa10-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="9aa10-491">Genellikle Yönlendirme, URL eşleştirme ile doğru yolu seçer.</span><span class="sxs-lookup"><span data-stu-id="9aa10-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="9aa10-492">`Order`İstekleri doğru yönlendirmek için yol özelliklerini ayarlamanız gerekiyorsa, uygulamanın yönlendirme şeması büyük olasılıkla istemciler için kafa karıştırıcı olur ve bakım için kırıcı olur.</span><span class="sxs-lookup"><span data-stu-id="9aa10-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="9aa10-493">Uygulamanın yönlendirme şemasını basitleştirecek şekilde arama yapın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="9aa10-494">Örnek uygulama, tek bir uygulama kullanarak birkaç yönlendirme senaryosunu göstermek için açık bir yol işleme sırası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="9aa10-495">Ancak, üretim uygulamalarında rota ayarlama uygulamalarından kaçınmaya çalışmalısınız `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="9aa10-496">Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-496"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9aa10-497">MVC konularındaki yol sırasıyla ilgili bilgiler, [Denetleyici eylemlerine yönlendirme sırasında mevcuttur: öznitelik yollarını sıralama](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="9aa10-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="9aa10-498">Model kurallar</span><span class="sxs-lookup"><span data-stu-id="9aa10-498">Model conventions</span></span>

<span data-ttu-id="9aa10-499"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>Sayfasına uygulanan [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için için bir temsilci ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-500">Tüm sayfalara bir rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="9aa10-501"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa yönlendirme modeli oluşturma sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="9aa10-502">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="9aa10-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-503">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `1` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="9aa10-504">Bu, örnek uygulamada aşağıdaki yol eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="9aa10-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="9aa10-505">İçin bir yol şablonu `TheContactPage/{text?}` , konusuna daha sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="9aa10-506">Iletişim sayfası yolu, () varsayılan sırasına sahiptir `null` `Order = 0` , bu nedenle `{globalTemplate?}` yol şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="9aa10-507">`{aboutTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-508">`{aboutTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-509">Hakkında sayfası istendiğinde `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue", () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="9aa10-510">`{otherPagesTemplate?}`Konuya daha sonra bir yol şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9aa10-511">`{otherPagesTemplate?}`Şablonuna bir verilebilir `Order` `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9aa10-512">*Sayfalar/otherpages* klasöründeki herhangi bir sayfa bir yol parametresi ile istendiğinde (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () öğesine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-513">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-514">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-514">Rely on routing to select the correct route.</span></span>

Razor<span data-ttu-id="9aa10-515"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>İçindeki hizmet KOLEKSIYONUNA MVC eklendiğinde ekleme gibi sayfa seçenekleri eklenir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-515"> Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9aa10-516">Örnek için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="9aa10-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="9aa10-517">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası, GlobalRouteValue 'un rota segmentiyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-520">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="9aa10-521"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Oluşturma ve <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sayfa uygulama modeli oluşturma sırasında uygulanan örnek koleksiyonuna eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="9aa10-522">Bu ve diğer kuralları konunun ilerleyen kısımlarında göstermek için, örnek uygulama bir `AddHeaderAttribute` sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="9aa10-523">Sınıf Oluşturucusu bir `name` dize ve bir `values` dize dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9aa10-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="9aa10-524">Bu değerler, `OnResultExecuting` bir yanıt üst bilgisi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="9aa10-525">Tam sınıf, konusunun ilerleyen kısımlarında [sayfa modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="9aa10-526">Örnek uygulama, `AddHeaderAttribute` `GlobalHeader` uygulama içindeki tüm sayfalara üst bilgi eklemek için sınıfını kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="9aa10-528">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri GlobalHeader 'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="9aa10-530">Tüm sayfalara bir işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="9aa10-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="9aa10-531"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Sayfa işleyici modelinin oluşturulması sırasında uygulanan örnek koleksiyonu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="9aa10-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="9aa10-533">Sayfa yolu eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-533">Page route action conventions</span></span>

<span data-ttu-id="9aa10-534"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>Sayfa yollarının yapılandırılması için genişletilebilirlik noktaları sağlamak üzere tasarlanan, çağıran kurallarından türetilen varsayılan yol modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="9aa10-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="9aa10-535">Klasör Yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-535">Folder route model convention</span></span>

<span data-ttu-id="9aa10-536"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Belirtilen klasörün altındaki tüm sayfalar için üzerinde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-537">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *diğer sayfalar* klasöründeki sayfalara bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="9aa10-538">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-539">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-540">*Sayfalar/otherpages* klasöründeki bir sayfa bir rota parametresi değeriyle isteniyorsa (örneğin, `/OtherPages/Page1/RouteDataValue` ), özelliğin ayarlanması nedeniyle "routedatavalue" `RouteData.Values["globalTemplate"]` () `Order = 1` ve Not `RouteData.Values["otherPagesTemplate"]` () içine yüklenir `Order = 2` `Order` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-541">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-542">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-543">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Diğer sayfalar klasöründeki Sayfa1, GlobalRouteValue ve OtherPagesRouteValue yol kesimiyle istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="9aa10-546">Sayfa yönlendirme modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="9aa10-546">Page route model convention</span></span>

<span data-ttu-id="9aa10-547"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-548">Örnek uygulama, `AddPageRouteModelConvention` `{aboutTemplate?}` hakkında sayfasına bir yol şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="9aa10-549">İçin <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> özelliği <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> olarak ayarlanır `2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9aa10-550">Bu, `{globalTemplate?}` tek bir rota değeri sağlandığında, (konusunda daha önce, konusunda daha önce olan `1` ) şablonun ilk yol veri değeri konumu için öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="9aa10-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9aa10-551">Hakkında sayfasında bir yol parametresi değeri varsa `/About/RouteDataValue` , `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["aboutTemplate"]` `Order = 2` özelliğin ayarlanması nedeniyle `Order` "routedatavalue" () içine () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9aa10-552">Mümkün olan yerlerde, `Order` ' yi ayarlayın `Order = 0` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9aa10-553">Doğru yolu seçmek için yönlendirmeyi güvenin.</span><span class="sxs-lookup"><span data-stu-id="9aa10-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9aa10-554">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![GlobalRouteValue ve AboutRouteValue için rota kesimlerle ilgili sayfa hakkında bilgi istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="9aa10-557">Sayfa yolu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9aa10-557">Configure a page route</span></span>

<span data-ttu-id="9aa10-558"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>Belirtilen sayfa yolundaki sayfaya bir yol yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="9aa10-559">Sayfa için oluşturulan bağlantılar belirtilen rotayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="9aa10-560">`AddPageRoute``AddPageRouteModelConvention`yolu oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="9aa10-561">Örnek uygulama, `/TheContactPage` *Contact. cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="9aa10-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="9aa10-562">Iletişim sayfasına, `/Contact` varsayılan yolu üzerinden de erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="9aa10-563">Örnek uygulamanın kişi sayfasına özel yolu, isteğe bağlı bir `text` yol segmentine () izin verir `{text?}` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="9aa10-564">Bu sayfa, `@page` ziyaretçinin yolunda sayfaya erişmesi durumunda bu isteğe bağlı segmenti de içerir `/Contact` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="9aa10-565">İşlenmiş sayfadaki **kişi** bağlantısı IÇIN oluşturulan URL 'nin güncelleştirilmiş yolu yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="9aa10-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Gezinti çubuğundaki örnek uygulama Iletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenmiş HTML 'deki kişi bağlantısının inceleniyor href 'in '/theContact tpage ' olarak ayarlandığını belirtir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="9aa10-568">Kendi normal rotasında, veya özel rotada kişi sayfasını ziyaret edin `/Contact` `/TheContactPage` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="9aa10-569">Ek bir `text` Rota kesimi sağlarsanız, sayfada SAĞLADıĞıNıZ HTML kodlu segment görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Edge tarayıcı, URL 'de ' TextValue ' öğesinin isteğe bağlı ' text ' yol segmentini sağlamaya yönelik örnek.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="9aa10-572">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="9aa10-572">Page model action conventions</span></span>

<span data-ttu-id="9aa10-573">Uygulayan varsayılan sayfa modeli sağlayıcısı, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanan kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="9aa10-574">Bu kurallar sayfa bulma ve işleme senaryolarını oluştururken ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="9aa10-575">Bu bölümdeki örneklerde, örnek uygulama `AddHeaderAttribute` bir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> yanıt üst bilgisi uygulayan olan bir sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="9aa10-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="9aa10-576">Kurallar kullanılarak, örnek bir klasördeki tüm sayfalara ve tek bir sayfaya özniteliğin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="9aa10-577">**Klasör uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-577">**Folder app model convention**</span></span>

<span data-ttu-id="9aa10-578"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Belirtilen klasör altındaki tüm sayfalar için örneklere bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="9aa10-579">Örnek, uygulamasının, `AddFolderApplicationModelConvention` `OtherPagesHeader` uygulamanın *diğer sayfalar* klasörünün içindeki sayfalara bir başlık ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9aa10-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="9aa10-580">Örnekteki Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Diğer sayfalar/Sayfa1 sayfasının yanıt üstbilgileri, OtherPagesHeader öğesinin eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="9aa10-582">**Sayfa uygulama modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="9aa10-582">**Page app model convention**</span></span>

<span data-ttu-id="9aa10-583"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Sayfa için belirtilen ada sahip bir eylemi çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="9aa10-584">Örnek, `AddPageApplicationModelConvention` hakkında sayfasına bir başlık ekleyerek öğesinin kullanımını gösterir `AboutHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="9aa10-585">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri AboutHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="9aa10-587">**Filtre yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-587">**Configure a filter**</span></span>

<span data-ttu-id="9aa10-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Belirtilen filtreyi uygulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="9aa10-589">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama bir lambda ifadesinde bir filtrenin nasıl uygulanacağını gösterir, bu da bir filtre döndüren bir fabrika olarak arka planda uygulandı:</span><span class="sxs-lookup"><span data-stu-id="9aa10-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="9aa10-590">Sayfa uygulama modeli, *diğer sayfalar* klasöründeki Page2 sayfasına yol açan parçaların göreli yolunu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="9aa10-591">Koşul geçerse, bir üst bilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="9aa10-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="9aa10-592">Aksi takdirde, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="9aa10-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="9aa10-593">`EmptyFilter`bir [eylem filtresidir](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="9aa10-594">Eylem filtreleri sayfalar tarafından yoksayıldığından, Razor `EmptyFilter` yol içermiyorsa hiçbir etkisi yoktur `OtherPages/Page2` .</span><span class="sxs-lookup"><span data-stu-id="9aa10-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="9aa10-595">Örnek Page2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header, Page2 için yanıta eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="9aa10-597">**Filtre fabrikası yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="9aa10-597">**Configure a filter factory**</span></span>

<span data-ttu-id="9aa10-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm sayfalara [filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikası yapılandırır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="9aa10-599">Örnek uygulama, uygulamanın sayfalarına iki değerli bir başlık ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanmanın bir örneğini sağlar `FilterFactoryHeader` :</span><span class="sxs-lookup"><span data-stu-id="9aa10-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="9aa10-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="9aa10-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="9aa10-601">Örnekteki hakkında daha fazla bilgi isteyin `localhost:5000/About` ve sonucu görüntülemek için üst bilgileri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="9aa10-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt üstbilgileri iki FilterFactoryHeader üst bilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="9aa10-603">MVC filtreleri ve sayfa filtresi (ıpagefilter)</span><span class="sxs-lookup"><span data-stu-id="9aa10-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="9aa10-604">[Action filters](xref:mvc/controllers/filters#action-filters) Razor Sayfalar işleyici YÖNTEMLERI kullandığından, MVC eylem filtreleri sayfalar tarafından yok sayılır Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="9aa10-605">Diğer MVC filtresi türleri şunlardır: [Yetkilendirme](xref:mvc/controllers/filters#authorization-filters), [özel durum](xref:mvc/controllers/filters#exception-filters), [kaynak](xref:mvc/controllers/filters#resource-filters)ve [sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="9aa10-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="9aa10-606">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="9aa10-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="9aa10-607">Sayfa filtresi ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ), sayfalar için geçerli olan bir filtredir Razor .</span><span class="sxs-lookup"><span data-stu-id="9aa10-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="9aa10-608">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="9aa10-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa10-609">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9aa10-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
