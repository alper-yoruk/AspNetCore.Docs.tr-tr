---
title: RazorASP.NET Core sayfaların filtre yöntemleri
author: Rick-Anderson
description: 'ASP.NET Core sayfalar için filtre yöntemleri oluşturmayı öğrenin Razor .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056835"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="d1f56-103">RazorASP.NET Core sayfaların filtre yöntemleri</span><span class="sxs-lookup"><span data-stu-id="d1f56-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1f56-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1f56-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1f56-105">Razor Sayfa filtreleri [ıpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [ıasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor sayfaların bir sayfa işleyicisi çalıştırılmadan önce ve sonra kod çalıştırmasına izin verir Razor .</span><span class="sxs-lookup"><span data-stu-id="d1f56-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="d1f56-106">Razor Sayfa filtreleri, tek sayfa işleyicisi yöntemlerine uygulanamazlar hariç [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzerdir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="d1f56-107">Razor Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="d1f56-107">Razor Page filters:</span></span>

* <span data-ttu-id="d1f56-108">Bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="d1f56-109">Model bağlama işlemi tamamlandıktan sonra işleyici metodu yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="d1f56-110">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="d1f56-111">, Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="d1f56-112">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="d1f56-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="d1f56-113">, [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulmuş Oluşturucu bağımlılıkları olabilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d1f56-114">Daha fazla bilgi için bkz. [Servicefilterattribute](../mvc/controllers/filters.md#servicefilterattribute) ve [typefilterattribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="d1f56-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="d1f56-115">Sayfa oluşturucular ve ara yazılım, bir işleyici yöntemi yürütmeden önce özel kod yürütmeyi etkinleştirirken, yalnızca Razor sayfa filtreleri <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> ve sayfasına erişimi etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="d1f56-116">Ara yazılım, `HttpContext` "sayfa bağlamına" değil, öğesine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="d1f56-117">Filtrelerin <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> , öğesine erişim sağlayan bir türetilmiş parametresi vardır `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d1f56-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="d1f56-118">Sayfa filtresi için bir örnek aşağıda verilmiştir: yanıta bir üst bilgi ekleyen [bir filtre özniteliği uygulama](#ifa) , oluşturucular veya ara yazılım ile gerçekleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="d1f56-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="d1f56-119">Sayfa bağlamına erişim, sayfanın ve bu modelin modeline erişimi içerir, yalnızca filtreler, işleyiciler veya bir sayfanın gövdesi çalıştırılırken kullanılabilir Razor .</span><span class="sxs-lookup"><span data-stu-id="d1f56-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="d1f56-120">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1f56-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1f56-121">Razor Sayfa filtreleri, genel olarak veya sayfa düzeyinde uygulanabilecek aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="d1f56-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="d1f56-122">Zaman uyumlu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-122">Synchronous methods:</span></span>

  * <span data-ttu-id="d1f56-123">[Onpagehandlerselected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d1f56-124">[Onpagehandlerexecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici Yöntemi yürütülmeden önce çağırılır, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="d1f56-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="d1f56-125">[Onpagehandleryürütüldü](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : işleyici yöntemi yürütüldükten sonra, eylem sonucundan önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="d1f56-126">Zaman uyumsuz yöntemler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="d1f56-127">[Onpagehandlerselectionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Handler yöntemi seçildikten sonra zaman uyumsuz olarak çağırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="d1f56-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d1f56-128">[Onpagehandlerexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Handler yöntemi çağrılmadan önce zaman uyumsuz olarak çağrıldı, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="d1f56-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="d1f56-129">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="d1f56-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d1f56-130">Çerçeve öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d1f56-131">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d1f56-132">Her iki arabirim de uygulanmışsa yalnızca zaman uyumsuz yöntemler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="d1f56-133">Aynı kural sayfalardaki geçersiz kılmalara uygulanır, her ikisine de değil, geçersiz kılmanın zaman uyumlu veya zaman uyumsuz sürümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="d1f56-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="d1f56-134">RazorSayfa filtrelerini küresel olarak Uygula</span><span class="sxs-lookup"><span data-stu-id="d1f56-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="d1f56-135">Aşağıdaki kod şunları uygular `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="d1f56-136">Yukarıdaki kodda, Kullanıcı `ProcessUserAgent.Write` Aracı dizesiyle birlikte çalışarak Kullanıcı tarafından sağlanan koddur.</span><span class="sxs-lookup"><span data-stu-id="d1f56-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="d1f56-137">Aşağıdaki kod, sınıfında şunları sunar `SampleAsyncPageFilter` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="d1f56-138">Aşağıdaki kod <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> , `SampleAsyncPageFilter` yalnızca */filmlerde* bulunan sayfalara uygulamak için çağırır:</span><span class="sxs-lookup"><span data-stu-id="d1f56-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="d1f56-139">Aşağıdaki kod zaman uyumlu olarak uygular `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="d1f56-140">Aşağıdaki kod şunları sunar `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="d1f56-141">RazorFiltre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama</span><span class="sxs-lookup"><span data-stu-id="d1f56-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="d1f56-142">Aşağıdaki kod, zaman uyumsuz Razor sayfa filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="d1f56-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="d1f56-143">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="d1f56-143">Implement a filter attribute</span></span>

<span data-ttu-id="d1f56-144">Yerleşik öznitelik tabanlı filtre filtresi, alt <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> sınıflı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="d1f56-145">Aşağıdaki filtre yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="d1f56-146">Aşağıdaki kod `AddHeader` özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="d1f56-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="d1f56-147">Üst bilgileri incelemek için tarayıcı geliştirici araçları gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="d1f56-148">**Yanıt üst bilgileri** altında `author: Rick` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="d1f56-149">Sıralamayı geçersiz kılma yönergeleri için bkz. [varsayılan sırayı geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="d1f56-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="d1f56-150">Filtre işlem hattının bir filtreden kısa devre dışı olması için bkz. [iptal ve kısa](xref:mvc/controllers/filters#cancellation-and-short-circuiting) devre.</span><span class="sxs-lookup"><span data-stu-id="d1f56-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="d1f56-151">Yetkilendir filtre özniteliği</span><span class="sxs-lookup"><span data-stu-id="d1f56-151">Authorize filter attribute</span></span>

<span data-ttu-id="d1f56-152">[Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği bir öğesine uygulanabilir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1f56-153">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1f56-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1f56-154">Razor Sayfa filtreleri [ıpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [ıasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor sayfaların bir sayfa işleyicisi çalıştırılmadan önce ve sonra kod çalıştırmasına izin verir Razor .</span><span class="sxs-lookup"><span data-stu-id="d1f56-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="d1f56-155">Razor Sayfa filtreleri, tek sayfa işleyicisi yöntemlerine uygulanamazlar hariç [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzerdir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="d1f56-156">Razor Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="d1f56-156">Razor Page filters:</span></span>

* <span data-ttu-id="d1f56-157">Bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="d1f56-158">Model bağlama işlemi tamamlandıktan sonra işleyici metodu yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="d1f56-159">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d1f56-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="d1f56-160">, Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="d1f56-161">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="d1f56-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="d1f56-162">Bir işleyici yöntemi sayfa Oluşturucusu veya ara yazılım kullanılarak yürütülmeden önce kod çalıştırılabilir, ancak yalnızca Razor sayfa filtrelerinin [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)'e erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="d1f56-163">Filtrelerin öğesine erişim sağlayan bir [Filtercontext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) türetilmiş parametresi vardır `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d1f56-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="d1f56-164">Örneğin, [bir filtre uygula özniteliği](#ifa) örneği yanıta, oluşturucular veya ara yazılım ile yapılamadığını belirten bir üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="d1f56-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="d1f56-165">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1f56-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1f56-166">Razor Sayfa filtreleri, genel olarak veya sayfa düzeyinde uygulanabilecek aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="d1f56-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="d1f56-167">Zaman uyumlu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-167">Synchronous methods:</span></span>

  * <span data-ttu-id="d1f56-168">[Onpagehandlerselected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d1f56-169">[Onpagehandlerexecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici Yöntemi yürütülmeden önce çağırılır, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="d1f56-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="d1f56-170">[Onpagehandleryürütüldü](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : işleyici yöntemi yürütüldükten sonra, eylem sonucundan önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="d1f56-171">Zaman uyumsuz yöntemler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="d1f56-172">[Onpagehandlerselectionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Handler yöntemi seçildikten sonra zaman uyumsuz olarak çağırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="d1f56-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="d1f56-173">[Onpagehandlerexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Handler yöntemi çağrılmadan önce zaman uyumsuz olarak çağrıldı, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="d1f56-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="d1f56-174">Her ikisini de değil, bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="d1f56-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="d1f56-175">Çerçeve öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d1f56-176">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d1f56-177">Her iki arabirim de uygulanmışsa yalnızca zaman uyumsuz yöntemler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="d1f56-178">Aynı kural sayfalardaki geçersiz kılmalara uygulanır, her ikisine de değil, geçersiz kılmanın zaman uyumlu veya zaman uyumsuz sürümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="d1f56-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="d1f56-179">RazorSayfa filtrelerini küresel olarak Uygula</span><span class="sxs-lookup"><span data-stu-id="d1f56-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="d1f56-180">Aşağıdaki kod şunları uygular `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="d1f56-181">Yukarıdaki kodda, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="d1f56-182">Uygulama için izleme bilgilerini sağlamak üzere örnekte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d1f56-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="d1f56-183">Aşağıdaki kod, sınıfında şunları sunar `SampleAsyncPageFilter` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="d1f56-184">Aşağıdaki kod, tüm sınıfı gösterir `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="d1f56-185">Aşağıdaki kod `AddFolderApplicationModelConvention` `SampleAsyncPageFilter` yalnızca */alt klasöründeki* sayfalara uygulamak için çağırır:</span><span class="sxs-lookup"><span data-stu-id="d1f56-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="d1f56-186">Aşağıdaki kod zaman uyumlu olarak uygular `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="d1f56-187">Aşağıdaki kod şunları sunar `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="d1f56-188">RazorFiltre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama</span><span class="sxs-lookup"><span data-stu-id="d1f56-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="d1f56-189">Aşağıdaki kod, zaman uyumlu Razor sayfa filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="d1f56-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="d1f56-190">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="d1f56-190">Implement a filter attribute</span></span>

<span data-ttu-id="d1f56-191">Yerleşik öznitelik tabanlı filtre [Onresultexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtresi, alt sınıflı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d1f56-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="d1f56-192">Aşağıdaki filtre yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="d1f56-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="d1f56-193">Aşağıdaki kod `AddHeader` özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="d1f56-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="d1f56-194">Sıralamayı geçersiz kılma yönergeleri için bkz. [varsayılan sırayı geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="d1f56-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="d1f56-195">Filtre işlem hattının bir filtreden kısa devre dışı olması için bkz. [iptal ve kısa](xref:mvc/controllers/filters#cancellation-and-short-circuiting) devre.</span><span class="sxs-lookup"><span data-stu-id="d1f56-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="d1f56-196">Yetkilendir filtre özniteliği</span><span class="sxs-lookup"><span data-stu-id="d1f56-196">Authorize filter attribute</span></span>

<span data-ttu-id="d1f56-197">[Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği bir öğesine uygulanabilir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="d1f56-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end