---
title: RazorASP.NET Core sayfaların filtre yöntemleri
author: Rick-Anderson
description: ASP.NET Core sayfalar için filtre yöntemleri oluşturmayı öğrenin Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
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
uid: razor-pages/filter
ms.openlocfilehash: b6d6585c0cbd52715c4192d4ab3bee756dbb41b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635053"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="b741a-103">RazorASP.NET Core sayfaların filtre yöntemleri</span><span class="sxs-lookup"><span data-stu-id="b741a-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b741a-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b741a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b741a-105">Razor Sayfa filtreleri [ıpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [ıasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor sayfaların bir sayfa işleyicisi çalıştırılmadan önce ve sonra kod çalıştırmasına izin verir Razor .</span><span class="sxs-lookup"><span data-stu-id="b741a-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="b741a-106">Razor Sayfa filtreleri, tek sayfa işleyicisi yöntemlerine uygulanamazlar hariç [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzerdir.</span><span class="sxs-lookup"><span data-stu-id="b741a-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="b741a-107">Razor Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="b741a-107">Razor Page filters:</span></span>

* <span data-ttu-id="b741a-108">Bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="b741a-109">Model bağlama işlemi tamamlandıktan sonra işleyici metodu yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="b741a-110">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="b741a-111">, Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="b741a-112">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="b741a-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="b741a-113">, [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulmuş Oluşturucu bağımlılıkları olabilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="b741a-114">Daha fazla bilgi için bkz. [Servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) ve [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="b741a-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="b741a-115">Sayfa oluşturucular ve ara yazılım, bir işleyici yöntemi yürütmeden önce özel kod yürütmeyi etkinleştirirken, yalnızca Razor sayfa filtreleri <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> ve sayfasına erişimi etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="b741a-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="b741a-116">Ara yazılım, `HttpContext` "sayfa bağlamına" değil, öğesine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="b741a-117">Filtrelerin <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> , öğesine erişim sağlayan bir türetilmiş parametresi vardır `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b741a-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="b741a-118">Sayfa filtresi için bir örnek aşağıda verilmiştir: yanıta bir üst bilgi ekleyen [bir filtre özniteliği uygulama](#ifa) , oluşturucular veya ara yazılım ile gerçekleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="b741a-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="b741a-119">Sayfa bağlamına erişim, sayfanın ve bu modelin modeline erişimi içerir, yalnızca filtreler, işleyiciler veya bir sayfanın gövdesi çalıştırılırken kullanılabilir Razor .</span><span class="sxs-lookup"><span data-stu-id="b741a-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="b741a-120">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b741a-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b741a-121">Razor Sayfa filtreleri, genel olarak veya sayfa düzeyinde uygulanabilecek aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="b741a-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="b741a-122">Zaman uyumlu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="b741a-122">Synchronous methods:</span></span>

  * <span data-ttu-id="b741a-123">[Onpagehandlerselected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="b741a-124">[Onpagehandlerexecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici Yöntemi yürütülmeden önce çağırılır, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="b741a-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="b741a-125">[Onpagehandleryürütüldü](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : işleyici yöntemi yürütüldükten sonra, eylem sonucundan önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="b741a-126">Zaman uyumsuz yöntemler:</span><span class="sxs-lookup"><span data-stu-id="b741a-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="b741a-127">[Onpagehandlerselectionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Handler yöntemi seçildikten sonra zaman uyumsuz olarak çağırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="b741a-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="b741a-128">[Onpagehandlerexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Handler yöntemi çağrılmadan önce zaman uyumsuz olarak çağrıldı, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="b741a-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="b741a-129">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="b741a-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="b741a-130">Çerçeve öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="b741a-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="b741a-131">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="b741a-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="b741a-132">Her iki arabirim de uygulanmışsa yalnızca zaman uyumsuz yöntemler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="b741a-133">Aynı kural sayfalardaki geçersiz kılmalara uygulanır, her ikisine de değil, geçersiz kılmanın zaman uyumlu veya zaman uyumsuz sürümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="b741a-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="b741a-134">RazorSayfa filtrelerini küresel olarak Uygula</span><span class="sxs-lookup"><span data-stu-id="b741a-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="b741a-135">Aşağıdaki kod şunları uygular `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="b741a-136">Yukarıdaki kodda, Kullanıcı `ProcessUserAgent.Write` Aracı dizesiyle birlikte çalışarak Kullanıcı tarafından sağlanan koddur.</span><span class="sxs-lookup"><span data-stu-id="b741a-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="b741a-137">Aşağıdaki kod, sınıfında şunları sunar `SampleAsyncPageFilter` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="b741a-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="b741a-138">Aşağıdaki kod <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> , `SampleAsyncPageFilter` yalnızca */filmlerde*bulunan sayfalara uygulamak için çağırır:</span><span class="sxs-lookup"><span data-stu-id="b741a-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="b741a-139">Aşağıdaki kod zaman uyumlu olarak uygular `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="b741a-140">Aşağıdaki kod şunları sunar `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="b741a-141">RazorFiltre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama</span><span class="sxs-lookup"><span data-stu-id="b741a-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="b741a-142">Aşağıdaki kod, zaman uyumsuz Razor sayfa filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="b741a-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="b741a-143">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="b741a-143">Implement a filter attribute</span></span>

<span data-ttu-id="b741a-144">Yerleşik öznitelik tabanlı filtre filtresi, alt <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> sınıflı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="b741a-145">Aşağıdaki filtre yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="b741a-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="b741a-146">Aşağıdaki kod `AddHeader` özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="b741a-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="b741a-147">Üst bilgileri incelemek için tarayıcı geliştirici araçları gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="b741a-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="b741a-148">**Yanıt üst bilgileri**altında `author: Rick` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b741a-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="b741a-149">Sıralamayı geçersiz kılma yönergeleri için bkz. [varsayılan sırayı geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="b741a-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="b741a-150">Filtre işlem hattının bir filtreden kısa devre dışı olması için bkz. [iptal ve kısa](xref:mvc/controllers/filters#cancellation-and-short-circuiting) devre.</span><span class="sxs-lookup"><span data-stu-id="b741a-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="b741a-151">Yetkilendir filtre özniteliği</span><span class="sxs-lookup"><span data-stu-id="b741a-151">Authorize filter attribute</span></span>

<span data-ttu-id="b741a-152">[Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği bir öğesine uygulanabilir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b741a-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b741a-153">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b741a-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b741a-154">Razor Sayfa filtreleri [ıpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) ve [ıasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) Razor sayfaların bir sayfa işleyicisi çalıştırılmadan önce ve sonra kod çalıştırmasına izin verir Razor .</span><span class="sxs-lookup"><span data-stu-id="b741a-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="b741a-155">Razor Sayfa filtreleri, tek sayfa işleyicisi yöntemlerine uygulanamazlar hariç [ASP.NET Core MVC eylem filtrelerine](xref:mvc/controllers/filters#action-filters)benzerdir.</span><span class="sxs-lookup"><span data-stu-id="b741a-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="b741a-156">Razor Sayfa filtreleri:</span><span class="sxs-lookup"><span data-stu-id="b741a-156">Razor Page filters:</span></span>

* <span data-ttu-id="b741a-157">Bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="b741a-158">Model bağlama işlemi tamamlandıktan sonra işleyici metodu yürütülmeden önce kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="b741a-159">İşleyici yöntemi yürütüldükten sonra kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b741a-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="b741a-160">, Bir sayfada veya genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="b741a-161">Belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="b741a-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="b741a-162">Bir işleyici yöntemi sayfa Oluşturucusu veya ara yazılım kullanılarak yürütülmeden önce kod çalıştırılabilir, ancak yalnızca Razor sayfa filtrelerinin [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)'e erişimi vardır.</span><span class="sxs-lookup"><span data-stu-id="b741a-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="b741a-163">Filtrelerin öğesine erişim sağlayan bir [Filtercontext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) türetilmiş parametresi vardır `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b741a-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="b741a-164">Örneğin, [bir filtre uygula özniteliği](#ifa) örneği yanıta, oluşturucular veya ara yazılım ile yapılamadığını belirten bir üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="b741a-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="b741a-165">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b741a-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b741a-166">Razor Sayfa filtreleri, genel olarak veya sayfa düzeyinde uygulanabilecek aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="b741a-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="b741a-167">Zaman uyumlu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="b741a-167">Synchronous methods:</span></span>

  * <span data-ttu-id="b741a-168">[Onpagehandlerselected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : bir işleyici yöntemi seçildikten sonra, ancak model bağlama gerçekleşmeden önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="b741a-169">[Onpagehandlerexecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Işleyici Yöntemi yürütülmeden önce çağırılır, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="b741a-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="b741a-170">[Onpagehandleryürütüldü](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : işleyici yöntemi yürütüldükten sonra, eylem sonucundan önce çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="b741a-171">Zaman uyumsuz yöntemler:</span><span class="sxs-lookup"><span data-stu-id="b741a-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="b741a-172">[Onpagehandlerselectionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Handler yöntemi seçildikten sonra zaman uyumsuz olarak çağırılır, ancak model bağlama gerçekleşmeden önce.</span><span class="sxs-lookup"><span data-stu-id="b741a-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="b741a-173">[Onpagehandlerexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Handler yöntemi çağrılmadan önce zaman uyumsuz olarak çağrıldı, model bağlama işlemi tamamlandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="b741a-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="b741a-174">Her ikisini de değil, bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="b741a-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="b741a-175">Çerçeve öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="b741a-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="b741a-176">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="b741a-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="b741a-177">Her iki arabirim de uygulanmışsa yalnızca zaman uyumsuz yöntemler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="b741a-178">Aynı kural sayfalardaki geçersiz kılmalara uygulanır, her ikisine de değil, geçersiz kılmanın zaman uyumlu veya zaman uyumsuz sürümünü uygular.</span><span class="sxs-lookup"><span data-stu-id="b741a-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="b741a-179">RazorSayfa filtrelerini küresel olarak Uygula</span><span class="sxs-lookup"><span data-stu-id="b741a-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="b741a-180">Aşağıdaki kod şunları uygular `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="b741a-181">Yukarıdaki kodda, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b741a-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="b741a-182">Uygulama için izleme bilgilerini sağlamak üzere örnekte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b741a-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="b741a-183">Aşağıdaki kod, sınıfında şunları sunar `SampleAsyncPageFilter` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="b741a-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="b741a-184">Aşağıdaki kod, tüm sınıfı gösterir `Startup` :</span><span class="sxs-lookup"><span data-stu-id="b741a-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="b741a-185">Aşağıdaki kod `AddFolderApplicationModelConvention` `SampleAsyncPageFilter` yalnızca */alt klasöründeki*sayfalara uygulamak için çağırır:</span><span class="sxs-lookup"><span data-stu-id="b741a-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="b741a-186">Aşağıdaki kod zaman uyumlu olarak uygular `IPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="b741a-187">Aşağıdaki kod şunları sunar `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="b741a-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="b741a-188">RazorFiltre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama</span><span class="sxs-lookup"><span data-stu-id="b741a-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="b741a-189">Aşağıdaki kod, zaman uyumlu Razor sayfa filtrelerini geçersiz kılar:</span><span class="sxs-lookup"><span data-stu-id="b741a-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="b741a-190">Filtre özniteliği uygulama</span><span class="sxs-lookup"><span data-stu-id="b741a-190">Implement a filter attribute</span></span>

<span data-ttu-id="b741a-191">Yerleşik öznitelik tabanlı filtre [Onresultexecutionasync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filtresi, alt sınıflı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b741a-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="b741a-192">Aşağıdaki filtre yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="b741a-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="b741a-193">Aşağıdaki kod `AddHeader` özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="b741a-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="b741a-194">Sıralamayı geçersiz kılma yönergeleri için bkz. [varsayılan sırayı geçersiz kılma](xref:mvc/controllers/filters#overriding-the-default-order) .</span><span class="sxs-lookup"><span data-stu-id="b741a-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="b741a-195">Filtre işlem hattının bir filtreden kısa devre dışı olması için bkz. [iptal ve kısa](xref:mvc/controllers/filters#cancellation-and-short-circuiting) devre.</span><span class="sxs-lookup"><span data-stu-id="b741a-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="b741a-196">Yetkilendir filtre özniteliği</span><span class="sxs-lookup"><span data-stu-id="b741a-196">Authorize filter attribute</span></span>

<span data-ttu-id="b741a-197">[Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) özniteliği bir öğesine uygulanabilir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b741a-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
