---
title: ASP.NET Core'daki Jilet Sayfaları için filtreleme yöntemleri
author: Rick-Anderson
description: ASP.NET Core'da Razor Pages için filtre yöntemlerini nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660757"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="dae2d-103">ASP.NET Core'daki Jilet Sayfaları için filtreleme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="dae2d-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dae2d-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dae2d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dae2d-105">Razor Page [filtreleri IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter,](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor Page işleyicisi çalıştırılmadan önce ve sonra Razor Page'in kod çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="dae2d-106">Razor Page filtreleri [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzer, ancak tek tek sayfa işleyici yöntemlerine uygulanamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="dae2d-107">Jilet Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="dae2d-107">Razor Page filters:</span></span>

* <span data-ttu-id="dae2d-108">İşleyici yöntemi seçildikten sonra kodu çalıştır, ancak model bağlama oluşmadan önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="dae2d-109">İşleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="dae2d-110">İşleyici yöntemi çalıştırdıktan sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="dae2d-111">Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="dae2d-112">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="dae2d-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="dae2d-113">[Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) tarafından doldurulan yapıcı bağımlılıklara sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="dae2d-114">Daha fazla bilgi için [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) ve [TypeFilterAttribute'a](/aspnet/core/mvc/controllers/filters#typefilterattribute)bakın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="dae2d-115">Sayfa oluşturucular ve ara yazılımlar işleyici yöntemi uygulanmadan önce özel kodun yürütülmesini sağlarken, yalnızca Razor Page filtreleri sayfaya ve sayfaya <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> erişimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="dae2d-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="dae2d-116">Middleware erişimi vardır `HttpContext`, ama "sayfa bağlamı".</span><span class="sxs-lookup"><span data-stu-id="dae2d-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="dae2d-117">Filtreler, ''ye erişim sağlayan türemiş bir <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametreye `HttpContext`sahiptir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="dae2d-118">Örneğin, [bir filtre özniteliği uygula](#ifa) örneği yanıta, oluşturucularla veya ara yazılımlarla yapılameyecek bir üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="dae2d-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="dae2d-119">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dae2d-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dae2d-120">Razor Page filtreleri, genel olarak veya sayfa düzeyinde uygulanabilen aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="dae2d-121">Senkron yöntemler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-121">Synchronous methods:</span></span>

  * <span data-ttu-id="dae2d-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Bir işleyici yöntemi seçildikten sonra çağrılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="dae2d-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="dae2d-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Eylem sonucundan önce işleyici yöntemi çalıştırdıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="dae2d-125">Asenkron yöntemler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="dae2d-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : İşleyici yöntemi seçildikten sonra eşsenkronize olarak adlandırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="dae2d-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Model bağlama tamamlandıktan sonra işleyici yöntemi çağrılmadan önce eşsenkronize olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="dae2d-128">Her **ikisini de** **değil,** filtre arabiriminin senkron veya async sürümünü uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="dae2d-129">Çerçeve, filtrenin async arabirimini uygulayıp uygulamadığını görmek için önce denetler ve eğer öyleyse, bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dae2d-130">Değilse, senkron arabirimin yöntemi(ler) çağırır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dae2d-131">Her iki arabirim de uygulanırsa, yalnızca async yöntemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="dae2d-132">Aynı kural sayfalardaki geçersiz kılmalar için de geçerlidir, geçersiz kılmanın eşzamanlı veya eşitlenmiş sürümünü uygulamak, her ikisini de değil.</span><span class="sxs-lookup"><span data-stu-id="dae2d-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="dae2d-133">Razor Page filtrelerini genel olarak uygulayın</span><span class="sxs-lookup"><span data-stu-id="dae2d-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="dae2d-134">Aşağıdaki kod `IAsyncPageFilter`uygular:</span><span class="sxs-lookup"><span data-stu-id="dae2d-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="dae2d-135">Önceki kodda, `ProcessUserAgent.Write` kullanıcı aracısı dizeilesiyle çalışan kullanıcı tarafından sağlanan kod vardır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="dae2d-136">Aşağıdaki kod `SampleAsyncPageFilter` sınıfta sağlar: `Startup`</span><span class="sxs-lookup"><span data-stu-id="dae2d-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="dae2d-137"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> */Movies'deki*yalnızca `SampleAsyncPageFilter` sayfalara uygulamak için aşağıdaki kod çağrıları:</span><span class="sxs-lookup"><span data-stu-id="dae2d-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="dae2d-138">Aşağıdaki kod senkron `IPageFilter`uyguluyor:</span><span class="sxs-lookup"><span data-stu-id="dae2d-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="dae2d-139">Aşağıdaki kod `SamplePageFilter`sağlar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="dae2d-140">Filtre yöntemlerini geçersiz kılarak Razor Page filtrelerini uygulayın</span><span class="sxs-lookup"><span data-stu-id="dae2d-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="dae2d-141">Aşağıdaki kod, eşzamanlı Jilet Sayfası filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="dae2d-142">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="dae2d-142">Implement a filter attribute</span></span>

<span data-ttu-id="dae2d-143">Yerleşik öznitelik tabanlı filtre <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filtresi alt sınıflanabilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="dae2d-144">Aşağıdaki filtre yanıta bir üstbilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="dae2d-145">Aşağıdaki kod özniteliği `AddHeader` uygular:</span><span class="sxs-lookup"><span data-stu-id="dae2d-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="dae2d-146">Üstbilgiincelemek için tarayıcı geliştirici araçları gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="dae2d-147">**Yanıt Üstbilgi** `author: Rick` altında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="dae2d-148">Bkz. Siparişin geçersiz kılınması yla ilgili yönergeler için [varsayılan sırayı geçersiz kılma.](xref:mvc/controllers/filters#overriding-the-default-order)</span><span class="sxs-lookup"><span data-stu-id="dae2d-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="dae2d-149">Bkz. Filtre boru hattını bir filtreden kısa devre yapmak için talimatlar için [İptal ve kısa devre.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)</span><span class="sxs-lookup"><span data-stu-id="dae2d-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="dae2d-150">Filtre özniteliğini yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="dae2d-150">Authorize filter attribute</span></span>

<span data-ttu-id="dae2d-151">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği aşağıdakilere `PageModel`uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dae2d-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dae2d-152">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dae2d-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dae2d-153">Razor Page [filtreleri IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [IAsyncPageFilter,](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor Page işleyicisi çalıştırılmadan önce ve sonra Razor Page'in kod çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="dae2d-154">Razor Page filtreleri [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzer, ancak tek tek sayfa işleyici yöntemlerine uygulanamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="dae2d-155">Jilet Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="dae2d-155">Razor Page filters:</span></span>

* <span data-ttu-id="dae2d-156">İşleyici yöntemi seçildikten sonra kodu çalıştır, ancak model bağlama oluşmadan önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="dae2d-157">İşleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="dae2d-158">İşleyici yöntemi çalıştırdıktan sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="dae2d-159">Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="dae2d-160">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="dae2d-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="dae2d-161">Kod, bir işleyici yöntemi sayfa oluşturucu veya ara yazılım kullanarak yürütülmeden önce çalıştırılabilir, ancak yalnızca Razor Page filtreleri [HttpContext'a](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)erişebilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="dae2d-162">Filtreler, [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) türetilmiş parametreye `HttpContext`erişebilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="dae2d-163">Örneğin, [bir filtre özniteliği uygula](#ifa) örneği yanıta, oluşturucularla veya ara yazılımlarla yapılameyecek bir üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="dae2d-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="dae2d-164">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dae2d-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dae2d-165">Razor Page filtreleri, genel olarak veya sayfa düzeyinde uygulanabilen aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="dae2d-166">Senkron yöntemler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-166">Synchronous methods:</span></span>

  * <span data-ttu-id="dae2d-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Bir işleyici yöntemi seçildikten sonra çağrılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="dae2d-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici yöntemi yürütülmeden önce, model bağlama tamamlandıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="dae2d-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Eylem sonucundan önce işleyici yöntemi çalıştırdıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="dae2d-170">Asenkron yöntemler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="dae2d-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : İşleyici yöntemi seçildikten sonra eşsenkronize olarak adlandırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="dae2d-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="dae2d-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Model bağlama tamamlandıktan sonra işleyici yöntemi çağrılmadan önce eşsenkronize olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="dae2d-173">Her **ikisini de** değil, filtre arabiriminin senkron veya async sürümünü uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dae2d-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="dae2d-174">Çerçeve, filtrenin async arabirimini uygulayıp uygulamadığını görmek için önce denetler ve eğer öyleyse, bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dae2d-175">Değilse, senkron arabirimin yöntemi(ler) çağırır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dae2d-176">Her iki arabirim de uygulanırsa, yalnızca async yöntemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="dae2d-177">Aynı kural sayfalardaki geçersiz kılmalar için de geçerlidir, geçersiz kılmanın eşzamanlı veya eşitlenmiş sürümünü uygulamak, her ikisini de değil.</span><span class="sxs-lookup"><span data-stu-id="dae2d-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="dae2d-178">Razor Page filtrelerini genel olarak uygulayın</span><span class="sxs-lookup"><span data-stu-id="dae2d-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="dae2d-179">Aşağıdaki kod `IAsyncPageFilter`uygular:</span><span class="sxs-lookup"><span data-stu-id="dae2d-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="dae2d-180">Önceki kodda, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="dae2d-181">Uygulama için iz bilgileri sağlamak için örnekte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dae2d-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="dae2d-182">Aşağıdaki kod `SampleAsyncPageFilter` sınıfta sağlar: `Startup`</span><span class="sxs-lookup"><span data-stu-id="dae2d-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="dae2d-183">Aşağıdaki kod sınıfının `Startup` tamamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="dae2d-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="dae2d-184">`AddFolderApplicationModelConvention` */subFolder'daki*yalnızca `SampleAsyncPageFilter` sayfalara uygulamak için aşağıdaki kod çağrıları:</span><span class="sxs-lookup"><span data-stu-id="dae2d-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="dae2d-185">Aşağıdaki kod senkron `IPageFilter`uyguluyor:</span><span class="sxs-lookup"><span data-stu-id="dae2d-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="dae2d-186">Aşağıdaki kod `SamplePageFilter`sağlar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="dae2d-187">Filtre yöntemlerini geçersiz kılarak Razor Page filtrelerini uygulayın</span><span class="sxs-lookup"><span data-stu-id="dae2d-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="dae2d-188">Aşağıdaki kod eşzamanlı Razor Page filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="dae2d-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="dae2d-189">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="dae2d-189">Implement a filter attribute</span></span>

<span data-ttu-id="dae2d-190">Yerleşik öznitelik tabanlı filtre [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtresi alt sınıflanabilir.</span><span class="sxs-lookup"><span data-stu-id="dae2d-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="dae2d-191">Aşağıdaki filtre yanıta bir üstbilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="dae2d-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="dae2d-192">Aşağıdaki kod özniteliği `AddHeader` uygular:</span><span class="sxs-lookup"><span data-stu-id="dae2d-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="dae2d-193">Bkz. Siparişin geçersiz kılınması yla ilgili yönergeler için [varsayılan sırayı geçersiz kılma.](xref:mvc/controllers/filters#overriding-the-default-order)</span><span class="sxs-lookup"><span data-stu-id="dae2d-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="dae2d-194">Bkz. Filtre boru hattını bir filtreden kısa devre yapmak için talimatlar için [İptal ve kısa devre.](xref:mvc/controllers/filters#cancellation-and-short-circuiting)</span><span class="sxs-lookup"><span data-stu-id="dae2d-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="dae2d-195">Filtre özniteliğini yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="dae2d-195">Authorize filter attribute</span></span>

<span data-ttu-id="dae2d-196">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği aşağıdakilere `PageModel`uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dae2d-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
