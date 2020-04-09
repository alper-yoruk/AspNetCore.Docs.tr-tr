---
title: ASP.NET Core'da Razor Pages rota ve uygulama kuralları
author: rick-anderson
description: Rota ve uygulama modeli sağlayıcısı kurallarının sayfa yönlendirme, bulma ve işlemeyi denetlemenize nasıl yardımcı olduğunu keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667862"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="ac7cb-103">ASP.NET Core'da Razor Pages rota ve uygulama kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac7cb-104">Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ac7cb-105">Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ac7cb-106">Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ac7cb-107">Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ac7cb-108">Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ac7cb-109">Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-109">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ac7cb-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac7cb-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ac7cb-111">Senaryo</span><span class="sxs-lookup"><span data-stu-id="ac7cb-111">Scenario</span></span> | <span data-ttu-id="ac7cb-112">Örnek gösterir ...</span><span class="sxs-lookup"><span data-stu-id="ac7cb-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ac7cb-113">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ac7cb-114">Sözleşmeler.Ekle</span><span class="sxs-lookup"><span data-stu-id="ac7cb-114">Conventions.Add</span></span><ul><li><span data-ttu-id="ac7cb-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ac7cb-118">Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ac7cb-119">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ac7cb-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ac7cb-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ac7cb-123">Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ac7cb-124">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ac7cb-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-127">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ac7cb-128">Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ac7cb-129">Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ac7cb-130">Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-130">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="ac7cb-131">Rota siparişi</span><span class="sxs-lookup"><span data-stu-id="ac7cb-131">Route order</span></span>

<span data-ttu-id="ac7cb-132">Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ac7cb-133">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-133">Order</span></span>            | <span data-ttu-id="ac7cb-134">Davranış</span><span class="sxs-lookup"><span data-stu-id="ac7cb-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ac7cb-135">-1</span><span class="sxs-lookup"><span data-stu-id="ac7cb-135">-1</span></span>               | <span data-ttu-id="ac7cb-136">Rota, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ac7cb-137">0</span><span class="sxs-lookup"><span data-stu-id="ac7cb-137">0</span></span>                | <span data-ttu-id="ac7cb-138">Sipariş belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-138">Order isn't specified (default value).</span></span> <span data-ttu-id="ac7cb-139">Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ac7cb-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ac7cb-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="ac7cb-141">Rota işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="ac7cb-142">Rota işleme sözleşme ile kurulur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="ac7cb-143">Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ac7cb-144">Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ac7cb-145">Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ac7cb-146">Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ac7cb-147">Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ac7cb-148">Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ac7cb-149">Uygulamanın yönlendirme düzenini basitleştirmeye çalış.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ac7cb-150">Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ac7cb-151">Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ac7cb-152">Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ac7cb-153">MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ac7cb-154">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-154">Model conventions</span></span>

<span data-ttu-id="ac7cb-155">Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-156">Tüm sayfalara rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="ac7cb-157">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ac7cb-158">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-159">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ac7cb-160">Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ac7cb-161">Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ac7cb-162">İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ac7cb-163">Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-164">Şablon `{aboutTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-165">`/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ac7cb-166">Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-167">Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-168">*Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-169">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-170">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-171">MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac7cb-172">Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ac7cb-173">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-176">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="ac7cb-177">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ac7cb-178">Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ac7cb-179">Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ac7cb-180">Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ac7cb-181">Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ac7cb-182">Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ac7cb-184">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-186">Tüm sayfalara işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ac7cb-187">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ac7cb-189">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-189">Page route action conventions</span></span>

<span data-ttu-id="ac7cb-190">Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ac7cb-191">Klasör rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-191">Folder route model convention</span></span>

<span data-ttu-id="ac7cb-192">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-193">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ac7cb-194">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-195">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-196">*Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-197">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-198">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-199">Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ac7cb-202">Sayfa rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-202">Page route model convention</span></span>

<span data-ttu-id="ac7cb-203">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-204">Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ac7cb-205">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-206">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-207">Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-208">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-209">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-210">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="ac7cb-213">Sayfa rotalarını özelleştirmek için parametre transformatörü kullanma</span><span class="sxs-lookup"><span data-stu-id="ac7cb-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="ac7cb-214">ASP.NET Core tarafından oluşturulan sayfa yolları bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="ac7cb-215">Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ac7cb-216">Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .</span><span class="sxs-lookup"><span data-stu-id="ac7cb-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ac7cb-217">Sayfa `PageRouteTransformerConvention` rotası modeli kuralı, bir uygulamada otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı segmentlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="ac7cb-218">Örneğin, */Pages/SubscriptionManagement/ViewAll.cshtml* adresindeki Razor Pages dosyasının rotası `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`' ndan .</span><span class="sxs-lookup"><span data-stu-id="ac7cb-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="ac7cb-219">`PageRouteTransformerConvention`yalnızca Razor Pages klasöründen ve dosya adından gelen bir sayfa rotasının otomatik olarak oluşturulan kesimlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="ac7cb-220">`@page` Yönergeyle eklenen rota bölümlerini dönüştürmez.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="ac7cb-221">Sözleşme, ekleyen yolları da <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dönüştürmez.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="ac7cb-222">Bir `PageRouteTransformerConvention` seçenek olarak `Startup.ConfigureServices`kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="ac7cb-223">Sayfa rotasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ac7cb-223">Configure a page route</span></span>

<span data-ttu-id="ac7cb-224">Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ac7cb-225">Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ac7cb-226">`AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ac7cb-227">Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ac7cb-228">İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ac7cb-229">Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ac7cb-230">Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ac7cb-231">İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ac7cb-234">Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ac7cb-235">Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ac7cb-238">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-238">Page model action conventions</span></span>

<span data-ttu-id="ac7cb-239">Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ac7cb-240">Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ac7cb-241">Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ac7cb-242">Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ac7cb-243">**Klasör uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-243">**Folder app model convention**</span></span>

<span data-ttu-id="ac7cb-244">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-245">Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ac7cb-246">Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ac7cb-248">**Sayfa uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-248">**Page app model convention**</span></span>

<span data-ttu-id="ac7cb-249">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-250">Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ac7cb-251">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ac7cb-253">**Filtreyi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-253">**Configure a filter**</span></span>

<span data-ttu-id="ac7cb-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ac7cb-255">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ac7cb-256">Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ac7cb-257">Koşul geçerse, bir üstbilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="ac7cb-258">Değilse, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ac7cb-259">`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ac7cb-260">Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ac7cb-261">Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ac7cb-263">**Filtre fabrikasını yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-263">**Configure a filter factory**</span></span>

<span data-ttu-id="ac7cb-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ac7cb-265">Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ac7cb-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ac7cb-267">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ac7cb-269">MVC Filtreler ve Sayfa filtresi (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ac7cb-270">Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ac7cb-271">Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ac7cb-272">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ac7cb-273">Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ac7cb-274">Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac7cb-275">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac7cb-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ac7cb-276">Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ac7cb-277">Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ac7cb-278">Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ac7cb-279">Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ac7cb-280">Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ac7cb-281">Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ac7cb-282">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac7cb-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ac7cb-283">Senaryo</span><span class="sxs-lookup"><span data-stu-id="ac7cb-283">Scenario</span></span> | <span data-ttu-id="ac7cb-284">Örnek gösterir ...</span><span class="sxs-lookup"><span data-stu-id="ac7cb-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ac7cb-285">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ac7cb-286">Sözleşmeler.Ekle</span><span class="sxs-lookup"><span data-stu-id="ac7cb-286">Conventions.Add</span></span><ul><li><span data-ttu-id="ac7cb-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ac7cb-290">Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ac7cb-291">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ac7cb-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ac7cb-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ac7cb-295">Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ac7cb-296">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ac7cb-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-299">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ac7cb-300">Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ac7cb-301">Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ac7cb-302">Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="ac7cb-303">Rota siparişi</span><span class="sxs-lookup"><span data-stu-id="ac7cb-303">Route order</span></span>

<span data-ttu-id="ac7cb-304">Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ac7cb-305">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-305">Order</span></span>            | <span data-ttu-id="ac7cb-306">Davranış</span><span class="sxs-lookup"><span data-stu-id="ac7cb-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ac7cb-307">-1</span><span class="sxs-lookup"><span data-stu-id="ac7cb-307">-1</span></span>               | <span data-ttu-id="ac7cb-308">Rota, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ac7cb-309">0</span><span class="sxs-lookup"><span data-stu-id="ac7cb-309">0</span></span>                | <span data-ttu-id="ac7cb-310">Sipariş belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-310">Order isn't specified (default value).</span></span> <span data-ttu-id="ac7cb-311">Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ac7cb-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ac7cb-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="ac7cb-313">Rota işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="ac7cb-314">Rota işleme sözleşme ile kurulur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="ac7cb-315">Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ac7cb-316">Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ac7cb-317">Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ac7cb-318">Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ac7cb-319">Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ac7cb-320">Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ac7cb-321">Uygulamanın yönlendirme düzenini basitleştirmeye çalış.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ac7cb-322">Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ac7cb-323">Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ac7cb-324">Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ac7cb-325">MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ac7cb-326">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-326">Model conventions</span></span>

<span data-ttu-id="ac7cb-327">Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-328">Tüm sayfalara rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="ac7cb-329">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ac7cb-330">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-331">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ac7cb-332">Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ac7cb-333">Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ac7cb-334">İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ac7cb-335">Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-336">Şablon `{aboutTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-337">`/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ac7cb-338">Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-339">Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-340">*Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-341">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-342">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-343">MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac7cb-344">Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ac7cb-345">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-348">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="ac7cb-349">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ac7cb-350">Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ac7cb-351">Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ac7cb-352">Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ac7cb-353">Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ac7cb-354">Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ac7cb-356">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-358">Tüm sayfalara işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ac7cb-359">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ac7cb-361">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-361">Page route action conventions</span></span>

<span data-ttu-id="ac7cb-362">Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ac7cb-363">Klasör rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-363">Folder route model convention</span></span>

<span data-ttu-id="ac7cb-364">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-365">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ac7cb-366">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-367">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-368">*Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-369">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-370">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-371">Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ac7cb-374">Sayfa rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-374">Page route model convention</span></span>

<span data-ttu-id="ac7cb-375">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-376">Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ac7cb-377">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-378">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-379">Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-380">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-381">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-382">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="ac7cb-385">Sayfa rotalarını özelleştirmek için parametre transformatörü kullanma</span><span class="sxs-lookup"><span data-stu-id="ac7cb-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="ac7cb-386">ASP.NET Core tarafından oluşturulan sayfa yolları bir parametre transformatörü kullanılarak özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="ac7cb-387">Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ac7cb-388">Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .</span><span class="sxs-lookup"><span data-stu-id="ac7cb-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ac7cb-389">Sayfa `PageRouteTransformerConvention` rotası modeli kuralı, bir uygulamada otomatik olarak oluşturulan sayfa yollarının klasör ve dosya adı segmentlerine bir parametre transformatörü uygular.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="ac7cb-390">Örneğin, */Pages/SubscriptionManagement/ViewAll.cshtml* adresindeki Razor Pages dosyasının rotası `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`' ndan .</span><span class="sxs-lookup"><span data-stu-id="ac7cb-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="ac7cb-391">`PageRouteTransformerConvention`yalnızca Razor Pages klasöründen ve dosya adından gelen bir sayfa rotasının otomatik olarak oluşturulan kesimlerini dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="ac7cb-392">`@page` Yönergeyle eklenen rota bölümlerini dönüştürmez.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="ac7cb-393">Sözleşme, ekleyen yolları da <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dönüştürmez.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="ac7cb-394">Bir `PageRouteTransformerConvention` seçenek olarak `Startup.ConfigureServices`kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="ac7cb-395">Sayfa rotasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ac7cb-395">Configure a page route</span></span>

<span data-ttu-id="ac7cb-396">Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ac7cb-397">Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ac7cb-398">`AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ac7cb-399">Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ac7cb-400">İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ac7cb-401">Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ac7cb-402">Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ac7cb-403">İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ac7cb-406">Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ac7cb-407">Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ac7cb-410">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-410">Page model action conventions</span></span>

<span data-ttu-id="ac7cb-411">Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ac7cb-412">Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ac7cb-413">Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ac7cb-414">Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ac7cb-415">**Klasör uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-415">**Folder app model convention**</span></span>

<span data-ttu-id="ac7cb-416">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-417">Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ac7cb-418">Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ac7cb-420">**Sayfa uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-420">**Page app model convention**</span></span>

<span data-ttu-id="ac7cb-421">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-422">Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ac7cb-423">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ac7cb-425">**Filtreyi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-425">**Configure a filter**</span></span>

<span data-ttu-id="ac7cb-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ac7cb-427">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ac7cb-428">Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ac7cb-429">Koşul geçerse, bir üstbilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="ac7cb-430">Değilse, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ac7cb-431">`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ac7cb-432">Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ac7cb-433">Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ac7cb-435">**Filtre fabrikasını yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-435">**Configure a filter factory**</span></span>

<span data-ttu-id="ac7cb-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ac7cb-437">Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ac7cb-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ac7cb-439">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ac7cb-441">MVC Filtreler ve Sayfa filtresi (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ac7cb-442">Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ac7cb-443">Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ac7cb-444">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ac7cb-445">Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ac7cb-446">Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac7cb-447">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac7cb-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ac7cb-448">Razor Pages uygulamalarında sayfa yönlendirmesini, keşfini ve işlemeyi denetlemek için sayfa [rotası ve uygulama modeli sağlayıcısı kurallarını](xref:mvc/controllers/application-model#conventions) nasıl kullanacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ac7cb-449">Tek tek sayfalar için özel sayfa rotalarını yapılandırmanız gerektiğinde, yönlendirmeyi daha sonra bu konuda açıklanan [AddPageRoute kuralıyla](#configure-a-page-route) sayfalara yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ac7cb-450">Bir sayfa rotası belirtmek için, rota bölümleri eklemek veya bir rotaya `@page` parametreler eklemek için sayfanın yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ac7cb-451">Daha fazla bilgi için [özel yollara](xref:razor-pages/index#custom-routes)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ac7cb-452">Rota bölümleri veya parametre adları olarak kullanılameyecek ayrılmış sözcükler vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ac7cb-453">Daha fazla bilgi için Bkz. [Yönlendirme: Ayrılmış yönlendirme adları.](xref:fundamentals/routing#reserved-routing-names)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ac7cb-454">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac7cb-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ac7cb-455">Senaryo</span><span class="sxs-lookup"><span data-stu-id="ac7cb-455">Scenario</span></span> | <span data-ttu-id="ac7cb-456">Örnek gösterir ...</span><span class="sxs-lookup"><span data-stu-id="ac7cb-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ac7cb-457">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ac7cb-458">Sözleşmeler.Ekle</span><span class="sxs-lookup"><span data-stu-id="ac7cb-458">Conventions.Add</span></span><ul><li><span data-ttu-id="ac7cb-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ac7cb-462">Uygulamanın sayfalarına bir rota şablonu ve üstbilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ac7cb-463">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ac7cb-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ac7cb-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ac7cb-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ac7cb-467">Bir klasördeki sayfalara ve tek bir sayfaya bir rota şablonu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ac7cb-468">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ac7cb-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ac7cb-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ac7cb-471">ConfigureFilter (filtre sınıfı, lambda ifadesi veya filtre fabrikası)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ac7cb-472">Klasördeki sayfalara üstbilgi ekleyin, tek bir sayfaya üstbilgi ekleyin ve uygulamanın sayfalarına üstbilgi eklemek için bir [filtre fabrikasını](xref:mvc/controllers/filters#ifilterfactory) yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ac7cb-473">Razor Pages kuralları <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` eklenir ve sınıftaki hizmet koleksiyonuna uzantı yöntemi kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ac7cb-474">Bu konuda aşağıdaki sözleşme örnekleri daha sonra açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="ac7cb-475">Rota siparişi</span><span class="sxs-lookup"><span data-stu-id="ac7cb-475">Route order</span></span>

<span data-ttu-id="ac7cb-476">Yollar işleme <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (rota eşleştirme) için bir belirtin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ac7cb-477">Sipariş verme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-477">Order</span></span>            | <span data-ttu-id="ac7cb-478">Davranış</span><span class="sxs-lookup"><span data-stu-id="ac7cb-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ac7cb-479">-1</span><span class="sxs-lookup"><span data-stu-id="ac7cb-479">-1</span></span>               | <span data-ttu-id="ac7cb-480">Rota, diğer rotalar işlenmeden önce işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ac7cb-481">0</span><span class="sxs-lookup"><span data-stu-id="ac7cb-481">0</span></span>                | <span data-ttu-id="ac7cb-482">Sipariş belirtilmemiş (varsayılan değer).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-482">Order isn't specified (default value).</span></span> <span data-ttu-id="ac7cb-483">Atamamak `Order` (`Order = null`) varsayılan `Order` olarak rotayı işleme için 0 (sıfır) olarak alır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ac7cb-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ac7cb-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="ac7cb-485">Rota işleme sırasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="ac7cb-486">Rota işleme sözleşme ile kurulur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="ac7cb-487">Rotalar sıralı sırayla işlenir (-1, &hellip; 0, 1, 2, n).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ac7cb-488">Rotalar aynı `Order`olduğunda, en özel rota önce daha az belirli rotalar tarafından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ac7cb-489">Aynı `Order` ve aynı sayıda parametreye sahip rotalar bir istek URL'si ile eşleştiğinde, rotalar eklenme sırasına göre <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>işlenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ac7cb-490">Mümkünse, kurulan bir rota işleme siparişine bağlı olarak kaçının.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ac7cb-491">Genellikle, yönlendirme URL eşleştirme ile doğru rotayı seçer.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ac7cb-492">Rota `Order` özelliklerini istekleri doğru yönlendirmek için ayarlamanız gerekiyorsa, uygulamanın yönlendirme düzeni büyük olasılıkla istemciler için kafa karıştırıcı ve bakımı için kırılgandır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ac7cb-493">Uygulamanın yönlendirme düzenini basitleştirmeye çalış.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ac7cb-494">Örnek uygulama, tek bir uygulamayı kullanarak birkaç yönlendirme senaryosu göstermek için açık bir rota işleme emri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ac7cb-495">Ancak, üretim uygulamalarında rota `Order` ayarlama uygulamasından kaçınmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ac7cb-496">Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ac7cb-497">MVC konularında rota sırası ile ilgili bilgiler [Yönlendirme'de eylemleri denetleyiciye sahiptir: Öznitelik yollarını sıralama.](xref:mvc/controllers/routing#ordering-attribute-routes)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ac7cb-498">Model kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-498">Model conventions</span></span>

<span data-ttu-id="ac7cb-499">Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> için geçerli [model kuralları](xref:mvc/controllers/application-model#conventions) eklemek için bir temsilci ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-500">Tüm sayfalara rota modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="ac7cb-501">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> rota modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örneklerin koleksiyonuoluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ac7cb-502">Örnek uygulama, `{globalTemplate?}` uygulamadaki tüm sayfalara bir rota şablonu ekler:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-503">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `1`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ac7cb-504">Bu, örnek uygulamada aşağıdaki rota eşleştirme davranışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ac7cb-505">Konunun daha `TheContactPage/{text?}` sonra için bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ac7cb-506">İlgili Kişi Sayfası rotasının `null` varsayılan`Order = 0`sırası (), `{globalTemplate?}` böylece rota şablonundan önce eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ac7cb-507">Daha `{aboutTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-508">Şablon `{aboutTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-509">`/About/RouteDataValue`Hakkında sayfa istendiğinde , "RouteDataValue" ( `RouteData.Values["globalTemplate"]` `Order = 1`) ve `RouteData.Values["aboutTemplate"]` (`Order = 2`) `Order` özelliğia ayarı nedeniyle yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ac7cb-510">Daha `{otherPagesTemplate?}` sonra konuya bir rota şablonu eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ac7cb-511">Şablon `{otherPagesTemplate?}` bir `Order` `2`verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ac7cb-512">*Sayfalar/Öteki Sayfalar* `/OtherPages/Page1/RouteDataValue`klasöründeki herhangi bir sayfa bir rota parametresi ile istendiğinde (örneğin, ),`Order = 2` `Order` özelliğin ayarlanması nedeniyle "RouteDataValue" `RouteData.Values["globalTemplate"]` (`Order = 1`) yüklendi ve ( ) yüklenmez. `RouteData.Values["otherPagesTemplate"]`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-513">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-514">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-515">MVC, hizmet koleksiyonuna eklendiğinde, ekleme <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>gibi Jilet `Startup.ConfigureServices`Sayfaları seçenekleri eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac7cb-516">Örneğin, [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ac7cb-517">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Hakkında sayfası GlobalRouteValue'in bir rota bölümüyle istenir.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-520">Tüm sayfalara uygulama modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="ac7cb-521">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> uygulaması modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> uygulanan örnekler in bir koleksiyonunu oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ac7cb-522">Bu ve diğer kuralları daha sonra konuyla göstermek `AddHeaderAttribute` için, örnek uygulama bir sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ac7cb-523">Sınıf oluşturucu bir `name` dize `values` ve dize dizisini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ac7cb-524">Bu değerler, bir `OnResultExecuting` yanıt üstbilgi ayarlamak için yönteminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ac7cb-525">Tam sınıf, daha sonra konusayfa [modeli eylem kuralları](#page-model-action-conventions) bölümünde gösterilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ac7cb-526">Örnek uygulama, `GlobalHeader` `AddHeaderAttribute` uygulamadaki tüm sayfalara bir üstbilgi eklemek için sınıfı kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ac7cb-528">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfasının yanıt başlıkları GlobalHeader eklendiğini gösterir.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ac7cb-530">Tüm sayfalara işleyici modeli kuralı ekleme</span><span class="sxs-lookup"><span data-stu-id="ac7cb-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ac7cb-531">Sayfa <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> işleyicisi <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> modeli oluşturma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> sırasında uygulanan örnekler in bir koleksiyon oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ac7cb-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ac7cb-533">Sayfa rotası eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-533">Page route action conventions</span></span>

<span data-ttu-id="ac7cb-534">Sayfa yollarını yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış çağırır kurallarından <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> türeyen varsayılan rota modeli sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ac7cb-535">Klasör rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-535">Folder route model convention</span></span>

<span data-ttu-id="ac7cb-536">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> tüm sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> için bir eylem çağıran bir oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-537">Örnek uygulama, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> `{otherPagesTemplate?}` *OtherPages* klasöründeki sayfalara bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ac7cb-538">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-539">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-540">*Sayfalar/Öteki Sayfalar* klasöründeki bir sayfa rota parametresi değeriyle `/OtherPages/Page1/RouteDataValue`(örneğin), `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` özelliğin ayarlanması nedeniyle () değil, () yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-541">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-542">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-543">Numunenin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages klasöründeki Page1, GlobalRouteValue ve OtherPagesRouteValue'nin bir rota segmenti ile birlikte istenir.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ac7cb-546">Sayfa rota modeli kuralı</span><span class="sxs-lookup"><span data-stu-id="ac7cb-546">Page route model convention</span></span>

<span data-ttu-id="ac7cb-547">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-548">Örnek uygulama, `AddPageRouteModelConvention` Hakkında `{aboutTemplate?}` sayfasına bir rota şablonu eklemek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ac7cb-549">Özellik <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> `2`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ac7cb-550">Bu, tek bir `{globalTemplate?}` rota değeri sağlandığında `1`ilk rota veri değeri konumu için şablona (konunun daha erken olarak ayarlandığı) öncelik verilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ac7cb-551">Hakkında sayfa bir rota parametre değeri ile `/About/RouteDataValue`istenirse , "RouteDataValue" `RouteData.Values["aboutTemplate"]` (`Order = 2``Order = 1`) içine `Order` yüklenir `RouteData.Values["globalTemplate"]` ve ( ) özelliği ayarı nedeniyle değil.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ac7cb-552">Mümkün olan her `Order`yerde, ' ile `Order = 0`sonuçlanır ' ı ayarlamayın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ac7cb-553">Doğru rotayı seçmek için yönlendirmeye güvenin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ac7cb-554">Numunenin Hakkında sayfasını isteyin `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ve sonucu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Hakkında sayfa GlobalRouteValue ve AboutRouteValue için rota segmentleri ile istenir.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="ac7cb-557">Sayfa rotasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ac7cb-557">Configure a page route</span></span>

<span data-ttu-id="ac7cb-558">Belirtilen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> sayfa yolundaki bir sayfaya rota yapılandırmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ac7cb-559">Sayfaya oluşturulan bağlantılar, belirtilen rotanızı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ac7cb-560">`AddPageRoute`rotayı oluşturmak için kullanır. `AddPageRouteModelConvention`</span><span class="sxs-lookup"><span data-stu-id="ac7cb-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ac7cb-561">Örnek uygulama `/TheContactPage` *Contact.cshtml*için bir yol oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ac7cb-562">İlgili Kişi sayfasına varsayılan `/Contact` rotası üzerinden de ulaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ac7cb-563">Örnek uygulamanın İletişim sayfasına özel rotası isteğe `text` bağlı`{text?}`bir rota segmentine izin verir ( ).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ac7cb-564">Sayfa, ziyaretçinin sayfaya `@page` `/Contact` kendi rotasından erişme ihtimaline karşı yönergesinde bu isteğe bağlı bölümü de içerir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ac7cb-565">İşlenen sayfadaki **Kişi** bağlantısı için oluşturulan URL'nin güncelleştirilmiş rotayı yansıttığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Navigasyon çubuğundaki örnek uygulama İletişim bağlantısı](razor-pages-conventions/_static/contact-link.png)

![İşlenen HTML'deki Kişi bağlantısının incelenmesi, href'in '/TheContactPage' olarak ayarlandığını gösterir](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ac7cb-568">Kişi sayfasını normal rotasından `/Contact`veya özel rotadan `/TheContactPage`ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ac7cb-569">Ek `text` bir rota kesimi sağlarsanız, sayfa sağladığınız HTML kodlanmış kesimi gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL'de 'TextValue' isteğe bağlı bir 'metin' rota segmenti sağlayan kenar tarayıcı örneği.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ac7cb-572">Sayfa modeli eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="ac7cb-572">Page model action conventions</span></span>

<span data-ttu-id="ac7cb-573">Uygulayan varsayılan sayfa modeli <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> sağlayıcısı, sayfa modellerini yapılandırmak için genişletilebilirlik noktaları sağlamak üzere tasarlanmış kuralları çağırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ac7cb-574">Bu kurallar, sayfa bulma ve işleme senaryoları oluşturma ve değiştirirken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ac7cb-575">Bu bölümdeki örnekler için, örnek `AddHeaderAttribute` uygulama bir sınıf <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>kullanır, bir , bir yanıt üstbilgisi geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ac7cb-576">Örnekler, özniteliğin bir klasördeki tüm sayfalara ve tek bir sayfaya nasıl uygulanacağı gösteriş eder.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ac7cb-577">**Klasör uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-577">**Folder app model convention**</span></span>

<span data-ttu-id="ac7cb-578">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> klasörün altındaki <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> tüm sayfalar için <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> örneklerde bir eylem çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ac7cb-579">Örnek, `AddFolderApplicationModelConvention` `OtherPagesHeader`uygulamanın *OtherPages* klasöründeki sayfalara bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ac7cb-580">Sonucu görüntülemek için örneğin Sayfa1 sayfasını isteyin `localhost:5000/OtherPages/Page1` ve üstbilgide inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 sayfasının yanıt başlıkları OtherPagesHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ac7cb-582">**Sayfa uygulaması modeli kuralı**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-582">**Page app model convention**</span></span>

<span data-ttu-id="ac7cb-583">Belirtilen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> ada sahip <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> sayfada bir eylem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> çağıran bir eylem oluşturmak ve eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ac7cb-584">Örnek, `AboutHeader`Hakkında sayfasına `AddPageApplicationModelConvention` bir üstbilgi ekleyerek kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ac7cb-585">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About sayfasının yanıt başlıkları AboutHeader'ın eklendiğini gösterir.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ac7cb-587">**Filtreyi yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-587">**Configure a filter**</span></span>

<span data-ttu-id="ac7cb-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>uygulamak için belirtilen filtreyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ac7cb-589">Bir filtre sınıfı uygulayabilirsiniz, ancak örnek uygulama, bir filtreyi döndüren bir fabrika olarak sahne arkasında uygulanan lambda ifadesinde filtrenin nasıl uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ac7cb-590">Sayfa uygulaması modeli, *OtherPages* klasöründeki Sayfa2 sayfasına giden kesimler için göreli yolu denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ac7cb-591">Koşul geçerse, bir üstbilgi eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="ac7cb-592">Değilse, `EmptyFilter` uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ac7cb-593">`EmptyFilter`bir [Eylem filtresidir.](xref:mvc/controllers/filters#action-filters)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ac7cb-594">Eylem filtreleri Razor Pages tarafından `EmptyFilter` göz ardı edildiklerinden, yol içermiyorsa `OtherPages/Page2`amaçlandığı gibi hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ac7cb-595">Numunenin Sayfa2 sayfasını isteyin `localhost:5000/OtherPages/Page2` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header Sayfa2 için yanıt eklenir.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ac7cb-597">**Filtre fabrikasını yapılandırma**</span><span class="sxs-lookup"><span data-stu-id="ac7cb-597">**Configure a filter factory**</span></span>

<span data-ttu-id="ac7cb-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>tüm Razor [Sayfalarına filtre](xref:mvc/controllers/filters) uygulamak için belirtilen fabrikayı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ac7cb-599">Örnek uygulama, `FilterFactoryHeader`uygulamanın sayfalarına iki değer içeren bir üstbilgi ekleyerek bir [filtre fabrikası](xref:mvc/controllers/filters#ifilterfactory) kullanma örneği sağlar:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ac7cb-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ac7cb-601">Numunenin Hakkında sayfasını isteyin `localhost:5000/About` ve sonucu görüntülemek için üstbilgiinceleyin:</span><span class="sxs-lookup"><span data-stu-id="ac7cb-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Hakkında sayfanın yanıt üstbilgisi iki FilterFactoryHeader üstbilgisinin eklendiğini gösterir.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ac7cb-603">MVC Filtreler ve Sayfa filtresi (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ac7cb-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ac7cb-604">Jilet Sayfaları işleyici yöntemlerini kullandığından, MVC [Eylem filtreleri](xref:mvc/controllers/filters#action-filters) Razor Pages tarafından yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ac7cb-605">Kullanabileceğiniz diğer MVC filtre türleri mevcuttur: [Yetkilendirme,](xref:mvc/controllers/filters#authorization-filters) [Özel Durum,](xref:mvc/controllers/filters#exception-filters) [Kaynak](xref:mvc/controllers/filters#resource-filters)ve [Sonuç](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="ac7cb-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ac7cb-606">Daha fazla bilgi için [Filtreler](xref:mvc/controllers/filters) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ac7cb-607">Sayfa filtresi<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) Jilet Sayfaları için geçerli olan bir filtredir.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ac7cb-608">Daha fazla bilgi [için, Razor Pages için Filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7cb-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac7cb-609">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac7cb-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
