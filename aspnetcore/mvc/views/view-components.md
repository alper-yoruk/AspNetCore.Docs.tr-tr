---
title: ASP.NET Core bileşenleri görüntüleme
author: rick-anderson
description: Görünüm bileşenlerinin ASP.NET Core nasıl kullanıldığını ve uygulamalara nasıl ekleneceğini öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060592"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="77497-103">ASP.NET Core bileşenleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="77497-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="77497-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77497-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="77497-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77497-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="77497-106">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="77497-106">View components</span></span>

<span data-ttu-id="77497-107">Görünüm bileşenleri kısmi görünümlere benzerdir, ancak çok daha güçlüdür.</span><span class="sxs-lookup"><span data-stu-id="77497-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="77497-108">Görüntüleme bileşenleri model bağlama kullanmaz ve yalnızca içine çağrılırken belirtilen verilere bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="77497-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="77497-109">Bu makale, denetleyiciler ve görünümler kullanılarak yazılmıştır, ancak bileşenleri görüntüleme, sayfalarla de çalışır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="77497-109">This article was written using controllers and views, but view components also work with :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="77497-110">Bir görünüm bileşeni:</span><span class="sxs-lookup"><span data-stu-id="77497-110">A view component:</span></span>

* <span data-ttu-id="77497-111">Tam yanıt yerine bir öbek işler.</span><span class="sxs-lookup"><span data-stu-id="77497-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="77497-112">, Bir denetleyici ve görünüm arasında aynı sorun ayrımı ve test edilebilirlik avantajları içerir.</span><span class="sxs-lookup"><span data-stu-id="77497-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="77497-113">Parametrelere ve iş mantığına sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="77497-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="77497-114">Genellikle bir düzen sayfasından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="77497-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="77497-115">Görünüm bileşenleri, kısmi bir görünüm için çok karmaşık olan işleme mantığınızı her yerde, örneğin:</span><span class="sxs-lookup"><span data-stu-id="77497-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="77497-116">Dinamik gezinti menüleri</span><span class="sxs-lookup"><span data-stu-id="77497-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="77497-117">Etiket bulutu (veritabanını sorgular)</span><span class="sxs-lookup"><span data-stu-id="77497-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="77497-118">Oturum açma paneli</span><span class="sxs-lookup"><span data-stu-id="77497-118">Login panel</span></span>
* <span data-ttu-id="77497-119">Alışveriş sepeti</span><span class="sxs-lookup"><span data-stu-id="77497-119">Shopping cart</span></span>
* <span data-ttu-id="77497-120">Son yayımlanan makaleler</span><span class="sxs-lookup"><span data-stu-id="77497-120">Recently published articles</span></span>
* <span data-ttu-id="77497-121">Normal blogdaki kenar çubuğu içeriği</span><span class="sxs-lookup"><span data-stu-id="77497-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="77497-122">Her sayfada işlenecek bir oturum açma paneli ve kullanıcının oturum açma durumuna bağlı olarak oturum açma veya oturum açma bağlantılarını gösterme</span><span class="sxs-lookup"><span data-stu-id="77497-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="77497-123">Bir görünüm bileşeni iki bölümden oluşur: Sınıf (genellikle [Viewcomponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)öğesinden türetilir) ve döndürdüğü sonuç (genellikle bir görünüm).</span><span class="sxs-lookup"><span data-stu-id="77497-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="77497-124">Denetleyiciler gibi bir görünüm bileşeni de bir POCO olabilir, ancak çoğu geliştirici, ' den türeterek kullanılabilir yöntemler ve özelliklerden faydalanmak ister `ViewComponent` .</span><span class="sxs-lookup"><span data-stu-id="77497-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="77497-125">Görünüm bileşenlerinin bir uygulamanın belirtimlerini karşılayıp karşılamadığını düşünürken, :::no-loc(Razor)::: bunun yerine bileşenleri kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="77497-125">When considering if view components meet an app's specifications, consider using :::no-loc(Razor)::: Components instead.</span></span> <span data-ttu-id="77497-126">:::no-loc(Razor)::: Bileşenler Ayrıca yeniden kullanılabilir kullanıcı arabirimi birimleri oluşturmak için biçimlendirmeyi C# koduyla birleştirir.</span><span class="sxs-lookup"><span data-stu-id="77497-126">:::no-loc(Razor)::: Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="77497-127">:::no-loc(Razor)::: Bileşenler, istemci tarafı UI mantığı ve kompozisyonu sağlarken geliştirici üretkenliği için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="77497-127">:::no-loc(Razor)::: Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="77497-128">Daha fazla bilgi için bkz. <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="77497-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="77497-129">Görünüm bileşeni oluşturma</span><span class="sxs-lookup"><span data-stu-id="77497-129">Creating a view component</span></span>

<span data-ttu-id="77497-130">Bu bölüm, bir görünüm bileşeni oluşturmak için üst düzey gereksinimleri içerir.</span><span class="sxs-lookup"><span data-stu-id="77497-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="77497-131">Makalenin ilerleyen kısımlarında, her adımı ayrıntılı olarak inceleyecek ve bir görünüm bileşeni oluşturacağız.</span><span class="sxs-lookup"><span data-stu-id="77497-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="77497-132">Görünüm bileşeni sınıfı</span><span class="sxs-lookup"><span data-stu-id="77497-132">The view component class</span></span>

<span data-ttu-id="77497-133">Bir görünüm bileşeni sınıfı, aşağıdakilerden herhangi biri tarafından oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="77497-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="77497-134">*Viewcomponent* 'dan türetme</span><span class="sxs-lookup"><span data-stu-id="77497-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="77497-135">Özniteliği özniteliğiyle bir sınıfı dekorasyon `[ViewComponent]` veya özniteliği ile bir sınıftan türetiliyor `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="77497-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="77497-136">Sonekin son ek *Viewcomponent* ile bittiği bir sınıf oluşturma</span><span class="sxs-lookup"><span data-stu-id="77497-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="77497-137">Denetleyiciler gibi, görünüm bileşenleri ortak, iç içe olmayan ve soyut olmayan sınıflar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77497-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="77497-138">Görünüm bileşeni adı, "ViewComponent" sonekini kaldıran sınıf adıdır.</span><span class="sxs-lookup"><span data-stu-id="77497-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="77497-139">Ayrıca özelliği kullanılarak açıkça belirtilebilir `ViewComponentAttribute.Name` .</span><span class="sxs-lookup"><span data-stu-id="77497-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="77497-140">Bir görünüm bileşeni Sınıfı:</span><span class="sxs-lookup"><span data-stu-id="77497-140">A view component class:</span></span>

* <span data-ttu-id="77497-141">Oluşturucu [bağımlılığı ekleme](../../fundamentals/dependency-injection.md) işlemini tamamen destekler</span><span class="sxs-lookup"><span data-stu-id="77497-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="77497-142">, Bir görünüm bileşeninde [filtre](../controllers/filters.md) kullanamayabilmeniz için denetleyicinin yaşam döngüsünde bir parçası almaz</span><span class="sxs-lookup"><span data-stu-id="77497-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="77497-143">Bileşen yöntemlerini görüntüle</span><span class="sxs-lookup"><span data-stu-id="77497-143">View component methods</span></span>

<span data-ttu-id="77497-144">Bir görünüm bileşeni, bir `InvokeAsync` `Task<IViewComponentResult>` veya döndüren zaman uyumlu bir yöntemde veya döndüren bir yöntemde mantığını tanımlar `Invoke` `IViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="77497-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="77497-145">Parametreler, model bağlamalarından değil doğrudan görünüm bileşeni çağrısından gelir.</span><span class="sxs-lookup"><span data-stu-id="77497-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="77497-146">Bir görünüm bileşeni hiçbir şekilde isteği doğrudan işlemez.</span><span class="sxs-lookup"><span data-stu-id="77497-146">A view component never directly handles a request.</span></span> <span data-ttu-id="77497-147">Genellikle, bir görünüm bileşeni bir modeli başlatır ve yöntemini çağırarak bir görünüme geçirir `View` .</span><span class="sxs-lookup"><span data-stu-id="77497-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="77497-148">Özet bölümünde bileşen yöntemlerini görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="77497-148">In summary, view component methods:</span></span>

* <span data-ttu-id="77497-149">Bir `InvokeAsync` `Task<IViewComponentResult>` veya döndüren zaman uyumlu bir yöntem döndüren bir yöntem tanımlayın `Invoke` `IViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="77497-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="77497-150">Genellikle bir modeli başlatır ve yöntemini çağırarak bir görünüme geçirir `ViewComponent` `View` .</span><span class="sxs-lookup"><span data-stu-id="77497-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="77497-151">Parametreler, HTTP değil çağırma yönteminden gelir.</span><span class="sxs-lookup"><span data-stu-id="77497-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="77497-152">Model bağlama yok.</span><span class="sxs-lookup"><span data-stu-id="77497-152">There's no model binding.</span></span>
* <span data-ttu-id="77497-153">Doğrudan bir HTTP uç noktası olarak erişilemez.</span><span class="sxs-lookup"><span data-stu-id="77497-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="77497-154">Bunlar, kodunuzun içinden çağrılır (genellikle bir görünümde).</span><span class="sxs-lookup"><span data-stu-id="77497-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="77497-155">Bir görünüm bileşeni hiçbir şekilde isteği hiçbir şekilde işlemez.</span><span class="sxs-lookup"><span data-stu-id="77497-155">A view component never handles a request.</span></span>
* <span data-ttu-id="77497-156">Geçerli HTTP isteğindeki tüm ayrıntılar yerine İmzada aşırı yüklendi.</span><span class="sxs-lookup"><span data-stu-id="77497-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="77497-157">Arama yolunu görüntüle</span><span class="sxs-lookup"><span data-stu-id="77497-157">View search path</span></span>

<span data-ttu-id="77497-158">Çalışma zamanı, görünümü aşağıdaki yollarda arar:</span><span class="sxs-lookup"><span data-stu-id="77497-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="77497-159">/Views/{Controller adı}/Components/{View bileşen adı}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="77497-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="77497-160">/Views/Shared/Components/{View bileşen adı}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="77497-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="77497-161">/Pages/Shared/Components/{View bileşen adı}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="77497-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="77497-162">Arama yolu, denetleyiciler + görünümler ve sayfalar kullanan projeler için geçerlidir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="77497-162">The search path applies to projects using controllers + views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="77497-163">Bir görünüm bileşeni için varsayılan görünüm adı *varsayılandır* , yani görünüm dosyanız genellikle *default. cshtml* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="77497-163">The default view name for a view component is *Default* , which means your view file will typically be named *Default.cshtml* .</span></span> <span data-ttu-id="77497-164">Görünüm bileşeni sonucunu oluştururken veya yöntemini çağırırken farklı bir görünüm adı belirtebilirsiniz `View` .</span><span class="sxs-lookup"><span data-stu-id="77497-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="77497-165">Görünüm dosyasını *default. cshtml* olarak yazmanız ve *görünümleri/paylaşılan/bileşenler/{görünüm bileşen adı}/{View Name}* yolunu kullanmanız önerilir.</span><span class="sxs-lookup"><span data-stu-id="77497-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="77497-166">`PriorityList`Bu örnekte kullanılan görünüm bileşeni görünüm bileşeni görünümü Için *Görünümler/paylaşılan/bileşenler/PriorityList/default. cshtml* kullanır.</span><span class="sxs-lookup"><span data-stu-id="77497-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="77497-167">Arama yolunu görüntüle</span><span class="sxs-lookup"><span data-stu-id="77497-167">Customize the view search path</span></span>

<span data-ttu-id="77497-168">Arama yolunu görüntüle ' yi özelleştirmek için, :::no-loc(Razor)::: <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> koleksiyonunu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77497-168">To customize the view search path, modify :::no-loc(Razor):::'s <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="77497-169">Örneğin, "/Components/{View bileşen adı}/{View Name}" yolu içinde görünümleri aramak için, koleksiyona yeni bir öğe ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77497-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="77497-170">Yukarıdaki kodda, "" yer tutucusu " {0} Bileşenler/{görünüm bileşeni adı}/{View Name}" yolunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="77497-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="77497-171">Bir görünüm bileşenini çağırma</span><span class="sxs-lookup"><span data-stu-id="77497-171">Invoking a view component</span></span>

<span data-ttu-id="77497-172">Görünüm bileşenini kullanmak için, aşağıdakileri bir görünüm içinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="77497-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="77497-173">Parametreleri `InvokeAsync` yöntemine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="77497-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="77497-174">`PriorityList`Makalede geliştirilen görünüm bileşeni, *Görünümler/Todo/Index. cshtml* görünüm dosyasından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="77497-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="77497-175">Aşağıdaki şekilde, `InvokeAsync` yöntemi iki parametreyle çağrılır:</span><span class="sxs-lookup"><span data-stu-id="77497-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="77497-176">Bir görünüm bileşenini etiket Yardımcısı olarak çağırma</span><span class="sxs-lookup"><span data-stu-id="77497-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="77497-177">ASP.NET Core 1,1 ve üzeri için bir görünüm bileşenini [etiket Yardımcısı](xref:mvc/views/tag-helpers/intro)olarak çağırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="77497-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="77497-178">Etiket Yardımcıları için Pascal özellikli sınıf ve Yöntem parametreleri, [Kebab durumlarına](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)çevrilir.</span><span class="sxs-lookup"><span data-stu-id="77497-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="77497-179">Bir görünüm bileşenini çağırmak için etiket Yardımcısı `<vc></vc>` öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="77497-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="77497-180">Görünüm bileşeni aşağıdaki gibi belirtilir:</span><span class="sxs-lookup"><span data-stu-id="77497-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="77497-181">Bir görünüm bileşenini etiket Yardımcısı olarak kullanmak için, yönergesini kullanarak görünüm bileşenini içeren derlemeyi kaydedin `@addTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="77497-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="77497-182">Görünüm bileşeniniz adlı bir derlemede ise `MyWebApp` , aşağıdaki yönergeyi *_ViewImports. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77497-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="77497-183">Görünüm bileşenine başvuran herhangi bir dosyaya bir görünüm bileşenini etiket Yardımcısı olarak kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77497-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="77497-184">Etiket yardımcılarını kaydetme hakkında daha fazla bilgi için bkz. [etiket Yardımcısı kapsamını yönetme](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) .</span><span class="sxs-lookup"><span data-stu-id="77497-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="77497-185">`InvokeAsync`Bu öğreticide kullanılan yöntem:</span><span class="sxs-lookup"><span data-stu-id="77497-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="77497-186">Etiket Yardımcısı biçimlendirmesinde:</span><span class="sxs-lookup"><span data-stu-id="77497-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="77497-187">Yukarıdaki örnekte, `PriorityList` Görünüm bileşeni olur `priority-list` .</span><span class="sxs-lookup"><span data-stu-id="77497-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="77497-188">Görünüm bileşenine yönelik parametreler, Kebab durumunda öznitelik olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="77497-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="77497-189">Bir görünüm bileşenini doğrudan bir denetleyiciden çağırma</span><span class="sxs-lookup"><span data-stu-id="77497-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="77497-190">Görünüm bileşenleri genellikle bir görünümden çağrılır, ancak bunları doğrudan bir denetleyici yönteminden çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77497-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="77497-191">Görüntüleme bileşenleri, denetleyiciler gibi uç noktaları tanımlamıyorsa, ' ın içeriğini döndüren bir denetleyici eylemini kolayca uygulayabilirsiniz `ViewComponentResult` .</span><span class="sxs-lookup"><span data-stu-id="77497-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="77497-192">Bu örnekte, görünüm bileşeni doğrudan denetleyiciden çağrılır:</span><span class="sxs-lookup"><span data-stu-id="77497-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="77497-193">İzlenecek yol: basit bir görünüm bileşeni oluşturma</span><span class="sxs-lookup"><span data-stu-id="77497-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="77497-194">Başlatıcı kodunu [indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), derleyin ve test edin.</span><span class="sxs-lookup"><span data-stu-id="77497-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="77497-195">Bu `ToDo` , *Yapılacaklar* öğelerinin listesini görüntüleyen bir denetleyiciyi olan basit bir projem.</span><span class="sxs-lookup"><span data-stu-id="77497-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![ToDos listesi](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="77497-197">ViewComponent sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="77497-197">Add a ViewComponent class</span></span>

<span data-ttu-id="77497-198">Bir *Viewcomponents* klasörü oluşturun ve aşağıdaki sınıfı ekleyin `PriorityListViewComponent` :</span><span class="sxs-lookup"><span data-stu-id="77497-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="77497-199">Koda notlar:</span><span class="sxs-lookup"><span data-stu-id="77497-199">Notes on the code:</span></span>

* <span data-ttu-id="77497-200">Görünüm bileşen sınıfları projedeki **herhangi bir** klasörde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="77497-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="77497-201">PriorityList **viewcomponent** sınıf adı, son ek **viewcomponent** ile sona ertiğinden, çalışma zamanı bir görünümden sınıf bileşenine başvururken "prioritylist" dizesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="77497-201">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent** , the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="77497-202">Daha sonra ayrıntılı olarak açıklayacağım.</span><span class="sxs-lookup"><span data-stu-id="77497-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="77497-203">`[ViewComponent]`Özniteliği bir görünüm bileşenine başvurmak için kullanılan adı değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="77497-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="77497-204">Örneğin, sınıfını adlandırdık `XYZ` ve özniteliği uygulamış olduğumuz `ViewComponent` :</span><span class="sxs-lookup"><span data-stu-id="77497-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="77497-205">`[ViewComponent]`Yukarıdaki özniteliği görüntüle bileşen seçicisine `PriorityList` bileşenle ilişkili görünümleri ararken adı kullanmasını ve bir görünümden sınıf bileşenine başvururken "PriorityList" dizesini kullanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="77497-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="77497-206">Daha sonra ayrıntılı olarak açıklayacağım.</span><span class="sxs-lookup"><span data-stu-id="77497-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="77497-207">Bileşen, veri bağlamını kullanılabilir hale getirmek için [bağımlılık ekleme](../../fundamentals/dependency-injection.md) işlemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="77497-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="77497-208">`InvokeAsync` bir görünümden çağrılabilen bir yöntemi gösterir ve rastgele sayıda bağımsız değişken alabilir.</span><span class="sxs-lookup"><span data-stu-id="77497-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="77497-209">`InvokeAsync`Yöntemi `ToDo` ve parametrelerini karşılayan öğe kümesini döndürür `isDone` `maxPriority` .</span><span class="sxs-lookup"><span data-stu-id="77497-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="77497-210">Görünüm bileşeni görünümünü oluşturma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="77497-210">Create the view component :::no-loc(Razor)::: view</span></span>

* <span data-ttu-id="77497-211">*Görünümler/paylaşılan/bileşenler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77497-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="77497-212">Bu klasör, adlandırılmış *Bileşenler* **olmalıdır** .</span><span class="sxs-lookup"><span data-stu-id="77497-212">This folder **must** be named *Components* .</span></span>

* <span data-ttu-id="77497-213">*Görünümler/paylaşılan/bileşenler/PriorityList* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77497-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="77497-214">Bu klasör adı, görünüm bileşen sınıfının adıyla ya da sınıfın adı eksi sonek ile eşleşmelidir (Bu kural izleniyorsa ve sınıf adında *Viewcomponent* sonekini kullandıysanız).</span><span class="sxs-lookup"><span data-stu-id="77497-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="77497-215">`ViewComponent`Özniteliğini kullandıysanız, sınıf adının öznitelik atamasını eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77497-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="77497-216">Bir *Görünümler/paylaşılan/bileşenler/PriorityList/default. cshtml* :::no-loc(Razor)::: görünümü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77497-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* :::no-loc(Razor)::: view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="77497-217">:::no-loc(Razor):::Görünüm bir listesini alır `TodoItem` ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="77497-217">The :::no-loc(Razor)::: view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="77497-218">Görünüm bileşeni yöntemi, `InvokeAsync` görünümün adını (örneğimizde olduğu gibi) geçirmezse, *varsayılan* olarak kurala göre görünüm adı için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77497-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="77497-219">Öğreticide daha sonra görünümün adının nasıl geçirileceğini göstereceğiz.</span><span class="sxs-lookup"><span data-stu-id="77497-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="77497-220">Belirli bir denetleyicinin varsayılan stilini geçersiz kılmak için denetleyiciye özgü görünüm klasörüne bir görünüm ekleyin (örneğin, *Görünümler/Todo/Components/PriorityList/default. cshtml)* .</span><span class="sxs-lookup"><span data-stu-id="77497-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span></span>

    <span data-ttu-id="77497-221">Görünüm bileşeni denetleyiciye özgü ise, denetleyiciyi denetleyiciye özgü klasöre ekleyebilirsiniz ( *Görünümler/Todo/bileşenler/PriorityList/default. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="77497-221">If the view component is controller-specific, you can add it to the controller-specific folder ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span></span>

* <span data-ttu-id="77497-222">`div`Priority listesi bileşenine, *Görünümler/Todo/index. cshtml* dosyasının altına bir çağrı içeren bir çağrı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77497-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="77497-223">Biçimlendirme, `@await Component.InvokeAsync` Görünüm bileşenlerini çağırma söz dizimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="77497-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="77497-224">İlk bağımsız değişken, çağırmak veya çağırmak istediğimiz bileşenin adıdır.</span><span class="sxs-lookup"><span data-stu-id="77497-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="77497-225">Sonraki parametreler bileşene geçirilir.</span><span class="sxs-lookup"><span data-stu-id="77497-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="77497-226">`InvokeAsync` rastgele sayıda bağımsız değişken alabilir.</span><span class="sxs-lookup"><span data-stu-id="77497-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="77497-227">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="77497-227">Test the app.</span></span> <span data-ttu-id="77497-228">Aşağıdaki görüntüde ToDo listesi ve öncelik öğeleri gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="77497-228">The following image shows the ToDo list and the priority items:</span></span>

![Yapılacaklar listesi ve öncelik öğeleri](view-components/_static/pi.png)

<span data-ttu-id="77497-230">Görünüm bileşenini doğrudan denetleyiciden de çağırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="77497-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![ındexvc eyleminden öncelikli öğeler](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="77497-232">Bir görünüm adı belirtme</span><span class="sxs-lookup"><span data-stu-id="77497-232">Specifying a view name</span></span>

<span data-ttu-id="77497-233">Karmaşık bir görünüm bileşeninin bazı koşullarda varsayılan olmayan bir görünüm belirtmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="77497-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="77497-234">Aşağıdaki kod, yönteminden "PVC" görünümünün nasıl ekleneceğini gösterir `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="77497-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="77497-235">`InvokeAsync`Sınıfında yöntemi güncelleştirin `PriorityListViewComponent` .</span><span class="sxs-lookup"><span data-stu-id="77497-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="77497-236">*Görünümleri/paylaşılan/bileşenler/prioritylist/default. cshtml* dosyasını *views/Shared/Components/PRIORITYLIST/PVC. cshtml* adlı bir görünüme kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="77497-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml* .</span></span> <span data-ttu-id="77497-237">PVC görünümünün kullanıldığını belirtmek için bir başlık ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77497-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="77497-238">Güncelleştirme *görünümleri/Todo/Index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="77497-238">Update *Views/ToDo/Index.cshtml* :</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="77497-239">Uygulamayı çalıştırın ve PVC görünümünü doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77497-239">Run the app and verify PVC view.</span></span>

![Öncelik görünümü bileşeni](view-components/_static/pvc.png)

<span data-ttu-id="77497-241">PVC görünümü işlenmemişse, görünüm bileşenini 4 veya daha yüksek bir önceliğe sahip olduğunuzu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77497-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="77497-242">Görünüm yolunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="77497-242">Examine the view path</span></span>

* <span data-ttu-id="77497-243">Öncelik görünümü döndürülmemesi için öncelik parametresini üç veya daha az olacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77497-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="77497-244">*Views/Todo/Components/PriorityList/default. cshtml* 'Yi *1default. cshtml* olarak geçici olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="77497-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml* .</span></span>
* <span data-ttu-id="77497-245">Uygulamayı test edin, şu hatayı alırsınız:</span><span class="sxs-lookup"><span data-stu-id="77497-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="77497-246">Görünümleri/ *Todo/bileşenler/PriorityList/1Default. cshtml* 'yi *views/Shared/Components/Prioritylist/default. cshtml* olarak kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="77497-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml* .</span></span>
* <span data-ttu-id="77497-247">Görünümün *paylaşılan* klasörden olduğunu göstermek için *paylaşılan* Todo görünümü bileşen görünümüne bir biçimlendirme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77497-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="77497-248">**Paylaşılan** bileşen görünümünü test edin.</span><span class="sxs-lookup"><span data-stu-id="77497-248">Test the **Shared** component view.</span></span>

![Paylaşılan bileşen görünümü ile ToDo çıkışı](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="77497-250">Sabit kodlanmış dizelerin önleme</span><span class="sxs-lookup"><span data-stu-id="77497-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="77497-251">Derleme zamanı güvenliğini istiyorsanız, sabit kodlanmış görünüm bileşeni adını sınıf adıyla değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77497-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="77497-252">"ViewComponent" soneki olmadan görünüm bileşenini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77497-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="77497-253">`using`Görünüm dosyanıza bir ifade ekleyin :::no-loc(Razor)::: ve `nameof` işlecini kullanın:</span><span class="sxs-lookup"><span data-stu-id="77497-253">Add a `using` statement to your :::no-loc(Razor)::: view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="77497-254">Zaman uyumlu iş gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="77497-254">Perform synchronous work</span></span>

<span data-ttu-id="77497-255">`Invoke`Zaman uyumsuz iş yapmanız gerekmiyorsa çerçeve zaman uyumlu bir yöntem çağırma işlemini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="77497-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="77497-256">Aşağıdaki yöntem, zaman uyumlu bir `Invoke` Görünüm bileşeni oluşturur:</span><span class="sxs-lookup"><span data-stu-id="77497-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="77497-257">Görünüm bileşeni :::no-loc(Razor)::: dosyası yöntemine geçirilen dizeleri listeler `Invoke` ( *Görünümler/Home/Components/Prioritylist/default. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="77497-257">The view component's :::no-loc(Razor)::: file lists the strings passed to the `Invoke` method ( *Views/Home/Components/PriorityList/Default.cshtml* ):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="77497-258">Görünüm bileşeni, :::no-loc(Razor)::: aşağıdaki yaklaşımlardan birini kullanarak bir dosyada (örneğin, *views/Home/Index. cshtml* ) çağrılır:</span><span class="sxs-lookup"><span data-stu-id="77497-258">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="77497-259">Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="77497-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="77497-260">Yaklaşımı kullanmak için <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> şunu çağırın `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="77497-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="77497-261">Görünüm bileşeni :::no-loc(Razor)::: ile bir dosyada (örneğin, *views/Home/Index. cshtml* ) çağrılır <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="77497-261">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="77497-262">Çağrı `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="77497-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="77497-263">Etiket Yardımcısını kullanmak için, yönergesini kullanarak görünüm bileşenini içeren derlemeyi kaydedin `@addTagHelper` (görünüm bileşeni adlı bir derlemede `MyWebApp` ):</span><span class="sxs-lookup"><span data-stu-id="77497-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="77497-264">Biçimlendirme dosyasında bileşen etiketini görüntüle yardımcısını kullanın :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="77497-264">Use the view component Tag Helper in the :::no-loc(Razor)::: markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="77497-265">Yöntemi imzası `PriorityList.Invoke` zaman uyumludur, ancak :::no-loc(Razor)::: biçimlendirme dosyasında metodunu bulur ve çağırır `Component.InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="77497-265">The method signature of `PriorityList.Invoke` is synchronous, but :::no-loc(Razor)::: finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="77497-266">Tüm görünüm bileşeni parametreleri gereklidir</span><span class="sxs-lookup"><span data-stu-id="77497-266">All view component parameters are required</span></span>

<span data-ttu-id="77497-267">Bir görünüm bileşenindeki her bir parametre gerekli bir özniteliktir.</span><span class="sxs-lookup"><span data-stu-id="77497-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="77497-268">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5011)bakın.</span><span class="sxs-lookup"><span data-stu-id="77497-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="77497-269">Herhangi bir parametre atlanırsa:</span><span class="sxs-lookup"><span data-stu-id="77497-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="77497-270">`InvokeAsync`Yöntem imzası eşleşmez, bu nedenle Yöntem yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="77497-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="77497-271">ViewComponent hiçbir biçimlendirmeyi işlemez.</span><span class="sxs-lookup"><span data-stu-id="77497-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="77497-272">Hata oluşturulmayacak.</span><span class="sxs-lookup"><span data-stu-id="77497-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77497-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77497-273">Additional resources</span></span>

* [<span data-ttu-id="77497-274">Görünümlere bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="77497-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
