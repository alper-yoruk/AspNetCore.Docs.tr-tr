---
title: ASP.NET Core filtreler
author: Rick-Anderson
description: Filtrelerin nasıl çalıştığını ve ASP.NET Core nasıl kullanılacağını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: eeae167286e793ecd5a547cea0142cf7d8014ece
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671788"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="88e97-103">ASP.NET Core filtreler</span><span class="sxs-lookup"><span data-stu-id="88e97-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88e97-104">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="88e97-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="88e97-105">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="88e97-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="88e97-106">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="88e97-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="88e97-107">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="88e97-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="88e97-108">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="88e97-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="88e97-109">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="88e97-110">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="88e97-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="88e97-111">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88e97-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="88e97-112">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="88e97-113">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="88e97-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="88e97-114">Filtreler doğrudan [ Razor bileşenlerle](xref:blazor/components/index)çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="88e97-115">Filtre, şu durumlarda bir bileşeni yalnızca dolaylı olarak etkileyebilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="88e97-116">Bileşen bir sayfa veya görünüme katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="88e97-117">Sayfa veya denetleyici/görünüm filtreyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="88e97-118">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) .</span><span class="sxs-lookup"><span data-stu-id="88e97-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="88e97-119">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="88e97-119">How filters work</span></span>

<span data-ttu-id="88e97-120">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="88e97-121">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve eylem çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="88e97-124">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="88e97-124">Filter types</span></span>

<span data-ttu-id="88e97-125">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="88e97-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="88e97-126">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="88e97-127">Yetkilendirme filtreleri, istek yetkilendirilmezse işlem hattı kısa devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="88e97-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="88e97-128">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="88e97-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="88e97-129">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="88e97-129">Run after authorization.</span></span>  
  * <span data-ttu-id="88e97-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> Filtre işlem hattının geri kalanında kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="88e97-131">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="88e97-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="88e97-133">[Eylem filtreleri](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="88e97-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="88e97-134">Kodu bir eylem yöntemi çağrıldıktan hemen önce ve sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="88e97-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="88e97-135">Bir eyleme geçirilen bağımsız değişkenleri değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="88e97-136">Eylemden döndürülen sonucu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="88e97-137">Sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="88e97-138">[Özel durum filtreleri](#exception-filters) , yanıt gövdesinin üzerine yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygular.</span><span class="sxs-lookup"><span data-stu-id="88e97-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="88e97-139">[Sonuç filtreleri](#result-filters) , eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="88e97-140">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="88e97-141">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="88e97-142">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="88e97-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="88e97-145">Uygulama</span><span class="sxs-lookup"><span data-stu-id="88e97-145">Implementation</span></span>

<span data-ttu-id="88e97-146">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="88e97-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="88e97-147">Zaman uyumlu filtreler, ardışık düzen aşamasından önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="88e97-148">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="88e97-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> , eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="88e97-150">Önceki kodda, [Mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) , [örnek indirmada](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)bir yardımcı programdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="88e97-151">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="88e97-152">Örneğin <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="88e97-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="88e97-153">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="88e97-154">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="88e97-154">Multiple filter stages</span></span>

<span data-ttu-id="88e97-155">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="88e97-156">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="88e97-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="88e97-157">Zaman uyumlu: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ve  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="88e97-158">Zaman uyumsuz: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="88e97-159">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="88e97-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="88e97-160">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="88e97-161">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="88e97-162">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="88e97-163">Gibi soyut sınıflar kullanırken <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemleri geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="88e97-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="88e97-164">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="88e97-164">Built-in filter attributes</span></span>

<span data-ttu-id="88e97-165">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="88e97-166">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="88e97-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-167">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="88e97-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="88e97-168">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="88e97-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="88e97-169">Üst bilgileri incelemek için [tarayıcı geliştirici araçları](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="88e97-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="88e97-170">**Yanıt üst bilgileri**altında `author: Rick Anderson` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="88e97-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="88e97-171">Aşağıdaki kod şunları uygular `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="88e97-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="88e97-172">Yapılandırma sistemindeki başlığı ve adı okur.</span><span class="sxs-lookup"><span data-stu-id="88e97-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="88e97-173">Önceki örnekten farklı olarak, aşağıdaki kod koda filtre parametrelerinin eklenmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="88e97-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="88e97-174">Başlık ve adı yanıt üstbilgisine ekler.</span><span class="sxs-lookup"><span data-stu-id="88e97-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-175">Yapılandırma seçenekleri, [Seçenekler deseninin](xref:fundamentals/configuration/options)kullanıldığı [yapılandırma sisteminden](xref:fundamentals/configuration/index) sağlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="88e97-176">Örneğin, dosyadaki *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="88e97-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="88e97-177">İçinde `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88e97-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="88e97-178">`PositionOptions`Sınıfı, yapılandırma alanı ile hizmet kapsayıcısına eklenir `"Position"` .</span><span class="sxs-lookup"><span data-stu-id="88e97-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="88e97-179">, `MyActionFilterAttribute` Hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="88e97-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="88e97-180">Aşağıdaki kod, sınıfını göstermektedir `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="88e97-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="88e97-181">Aşağıdaki kod `MyActionFilterAttribute` yöntemi için geçerlidir `Index2` :</span><span class="sxs-lookup"><span data-stu-id="88e97-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="88e97-182">**Yanıt üst bilgileri**altında `author: Rick Anderson` , ve `Editor: Joe Smith` `Sample/Index2` uç nokta çağrıldığında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="88e97-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="88e97-183">Aşağıdaki kod, `MyActionFilterAttribute` ve `AddHeaderAttribute` Razor sayfasını sayfasına uygular:</span><span class="sxs-lookup"><span data-stu-id="88e97-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="88e97-184">Filtreler Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="88e97-185">Bunlar Razor sayfa modeline ya da genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="88e97-186">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88e97-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="88e97-187">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="88e97-188">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="88e97-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="88e97-189">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="88e97-190">Bir denetleyici eyleminde bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="88e97-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="88e97-191">Filtre öznitelikleri, Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="88e97-192">Bir denetleyici veya sayfada bir özniteliği kullanma Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="88e97-193">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler, Eylemler ve sayfalar için genel olarak Razor :</span><span class="sxs-lookup"><span data-stu-id="88e97-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="88e97-194">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="88e97-194">Default order of execution</span></span>

<span data-ttu-id="88e97-195">İşlem hattının belirli bir aşamasına ilişkin birden çok filtre olduğunda, kapsam varsayılan filtre yürütme sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="88e97-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="88e97-196">Genel filtreler kapsayan sınıf filtreleri, bu da kapsayan Yöntem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="88e97-197">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="88e97-198">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="88e97-198">The filter sequence:</span></span>

* <span data-ttu-id="88e97-199">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="88e97-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="88e97-200">Denetleyicinin ve sayfa filtrelerinin *öncesindeki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="88e97-201">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="88e97-202">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="88e97-203">Denetleyicinin ve sayfa filtrelerinin *sonraki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="88e97-204">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="88e97-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="88e97-205">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="88e97-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="88e97-206">Sequence</span><span class="sxs-lookup"><span data-stu-id="88e97-206">Sequence</span></span> | <span data-ttu-id="88e97-207">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="88e97-207">Filter scope</span></span> | <span data-ttu-id="88e97-208">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="88e97-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="88e97-209">1</span><span class="sxs-lookup"><span data-stu-id="88e97-209">1</span></span> | <span data-ttu-id="88e97-210">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-211">2</span><span class="sxs-lookup"><span data-stu-id="88e97-211">2</span></span> | <span data-ttu-id="88e97-212">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="88e97-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="88e97-213">3</span><span class="sxs-lookup"><span data-stu-id="88e97-213">3</span></span> | <span data-ttu-id="88e97-214">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-215">4</span><span class="sxs-lookup"><span data-stu-id="88e97-215">4</span></span> | <span data-ttu-id="88e97-216">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="88e97-217">5</span><span class="sxs-lookup"><span data-stu-id="88e97-217">5</span></span> | <span data-ttu-id="88e97-218">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="88e97-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="88e97-219">6</span><span class="sxs-lookup"><span data-stu-id="88e97-219">6</span></span> | <span data-ttu-id="88e97-220">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="88e97-221">Denetleyici düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-221">Controller level filters</span></span>

<span data-ttu-id="88e97-222">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="88e97-223">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="88e97-223">These methods:</span></span>

* <span data-ttu-id="88e97-224">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="88e97-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="88e97-225">`OnActionExecuting` , eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="88e97-226">`OnActionExecuted` tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="88e97-227">`OnActionExecutionAsync` , eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="88e97-228">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="88e97-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="88e97-229">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="88e97-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="88e97-230">The `TestController`:</span></span>

* <span data-ttu-id="88e97-231">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="88e97-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="88e97-232">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="88e97-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="88e97-233">' A gitme `https://localhost:5001/Test/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="88e97-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="88e97-234">Denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini olarak ayarlar `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="88e97-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="88e97-235">Denetleyici düzeyi filtreleri metotlara uygulandıktan sonra çalıştırılacak şekilde ayarlanamaz. **not**</span><span class="sxs-lookup"><span data-stu-id="88e97-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="88e97-236">Sıra, sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="88e97-236">Order is explained in the next section.</span></span>

<span data-ttu-id="88e97-237">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="88e97-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="88e97-238">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="88e97-238">Overriding the default order</span></span>

<span data-ttu-id="88e97-239">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="88e97-240">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="88e97-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="88e97-241">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="88e97-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="88e97-242">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="88e97-243">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="88e97-244">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="88e97-245">Aşağıdaki denetleyicide iki eylem filtresini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="88e97-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="88e97-246">İçinde genel bir filtre eklenir `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88e97-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="88e97-247">3 filtre aşağıdaki sırayla çalışır:</span><span class="sxs-lookup"><span data-stu-id="88e97-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="88e97-248">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="88e97-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="88e97-249">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="88e97-250">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="88e97-251">Daha önce belirtildiği gibi, denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini `int.MinValue` yerleşik filtreler için olarak ayarlar, `Order` sıfır olmayan bir değere ayarlanmadığı sürece kapsam sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="88e97-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="88e97-252">Önceki kodda, `MySampleActionFilter` `MyAction2FilterAttribute` Denetleyici kapsamı olan, daha önce çalışması için genel kapsama sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88e97-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="88e97-253">`MyAction2FilterAttribute`Önce çalıştırmak için, sırayı şu şekilde ayarlayın `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="88e97-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="88e97-254">Önce genel filtrenin çalışmasını sağlamak için şu `MySampleActionFilter` şekilde ayarlayın `Order` `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="88e97-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="88e97-255">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="88e97-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="88e97-256">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="88e97-257">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="88e97-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-258">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="88e97-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="88e97-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="88e97-260">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="88e97-261">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="88e97-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="88e97-262">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="88e97-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="88e97-263">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="88e97-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="88e97-264">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="88e97-265">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="88e97-265">Dependency injection</span></span>

<span data-ttu-id="88e97-266">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="88e97-267">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="88e97-268">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="88e97-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="88e97-269">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="88e97-269">A type-activated filter means:</span></span>

* <span data-ttu-id="88e97-270">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88e97-270">An instance is created for each request.</span></span>
* <span data-ttu-id="88e97-271">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="88e97-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="88e97-272">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="88e97-273">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="88e97-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="88e97-274">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="88e97-275">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="88e97-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="88e97-276">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="88e97-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="88e97-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="88e97-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="88e97-278">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="88e97-279">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-279">Loggers are available from DI.</span></span> <span data-ttu-id="88e97-280">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88e97-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="88e97-281">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="88e97-282">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="88e97-282">Logging added to filters:</span></span>

* <span data-ttu-id="88e97-283">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="88e97-284">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="88e97-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="88e97-285">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="88e97-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="88e97-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="88e97-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="88e97-287">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88e97-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="88e97-288">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="88e97-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="88e97-289">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="88e97-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="88e97-290">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="88e97-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="88e97-291">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="88e97-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="88e97-292">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="88e97-293">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="88e97-294">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="88e97-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="88e97-295">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88e97-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="88e97-296">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="88e97-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="88e97-297">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="88e97-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="88e97-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="88e97-299">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="88e97-300">`CreateInstance` belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="88e97-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="88e97-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="88e97-301">TypeFilterAttribute</span></span>

<span data-ttu-id="88e97-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="88e97-303">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="88e97-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="88e97-304">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="88e97-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="88e97-305">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="88e97-306">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="88e97-307">`TypeFilterAttribute` isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="88e97-308">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="88e97-309">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="88e97-310">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="88e97-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="88e97-311">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="88e97-312">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="88e97-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="88e97-313">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-313">Authorization filters</span></span>

<span data-ttu-id="88e97-314">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-314">Authorization filters:</span></span>

* <span data-ttu-id="88e97-315">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="88e97-316">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-316">Control access to action methods.</span></span>
* <span data-ttu-id="88e97-317">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="88e97-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="88e97-318">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="88e97-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="88e97-319">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="88e97-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="88e97-320">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="88e97-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="88e97-321">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-321">Calls the authorization system.</span></span>
* <span data-ttu-id="88e97-322">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-322">Does not authorize requests.</span></span>

<span data-ttu-id="88e97-323">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="88e97-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="88e97-324">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="88e97-324">The exception will not be handled.</span></span>
* <span data-ttu-id="88e97-325">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="88e97-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="88e97-326">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="88e97-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="88e97-327">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="88e97-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="88e97-328">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-328">Resource filters</span></span>

<span data-ttu-id="88e97-329">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-329">Resource filters:</span></span>

* <span data-ttu-id="88e97-330"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="88e97-331">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="88e97-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="88e97-332">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="88e97-333">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="88e97-334">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="88e97-335">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-335">Resource filter examples:</span></span>

* <span data-ttu-id="88e97-336">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="88e97-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="88e97-337">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="88e97-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="88e97-338">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="88e97-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="88e97-339">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="88e97-340">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-340">Action filters</span></span>

<span data-ttu-id="88e97-341">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="88e97-342">Razor Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="88e97-343">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="88e97-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="88e97-344">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-344">Action filters:</span></span>

* <span data-ttu-id="88e97-345"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="88e97-346">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="88e97-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="88e97-347">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="88e97-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="88e97-348">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="88e97-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -girişlerin bir eylem yöntemine okunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="88e97-350"><xref:Microsoft.AspNetCore.Mvc.Controller> -denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="88e97-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="88e97-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="88e97-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="88e97-352">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="88e97-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="88e97-353">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="88e97-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="88e97-354">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="88e97-355">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="88e97-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="88e97-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="88e97-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="88e97-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="88e97-358">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-358">Setting this property to null:</span></span>

  * <span data-ttu-id="88e97-359">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="88e97-360">`Result` eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="88e97-361">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="88e97-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="88e97-362">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="88e97-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="88e97-363">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="88e97-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="88e97-364">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="88e97-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="88e97-365">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="88e97-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="88e97-366">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="88e97-367">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88e97-367">Validate model state.</span></span>
* <span data-ttu-id="88e97-368">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="88e97-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="88e97-369">Öznitelik ile ek açıklama eklenmiş denetleyiciler `[ApiController]` model durumunu otomatik olarak doğrular ve bir 400 yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="88e97-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="88e97-370">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="88e97-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="88e97-371">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="88e97-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="88e97-372">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="88e97-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="88e97-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="88e97-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="88e97-375">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="88e97-376">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="88e97-377">`ActionExecutedContext.Result` eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="88e97-378">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-378">Exception filters</span></span>

<span data-ttu-id="88e97-379">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-379">Exception filters:</span></span>

* <span data-ttu-id="88e97-380"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="88e97-381">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="88e97-382">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="88e97-383">Aşağıdaki kod özel durum filtresini sınar:</span><span class="sxs-lookup"><span data-stu-id="88e97-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="88e97-384">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-384">Exception filters:</span></span>

* <span data-ttu-id="88e97-385">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="88e97-385">Don't have before and after events.</span></span>
* <span data-ttu-id="88e97-386"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="88e97-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="88e97-387">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="88e97-388">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="88e97-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="88e97-389">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="88e97-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="88e97-390">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="88e97-390">This stops propagation of the exception.</span></span> <span data-ttu-id="88e97-391">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="88e97-392">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-392">Only an action filter can do that.</span></span>

<span data-ttu-id="88e97-393">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-393">Exception filters:</span></span>

* <span data-ttu-id="88e97-394">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="88e97-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="88e97-395">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="88e97-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="88e97-396">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="88e97-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="88e97-397">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="88e97-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="88e97-398">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="88e97-399">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="88e97-400">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-400">Result filters</span></span>

<span data-ttu-id="88e97-401">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-401">Result filters:</span></span>

* <span data-ttu-id="88e97-402">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="88e97-402">Implement an interface:</span></span>
  * <span data-ttu-id="88e97-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="88e97-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="88e97-405">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="88e97-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="88e97-406">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="88e97-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="88e97-407">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="88e97-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="88e97-408">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="88e97-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="88e97-409">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="88e97-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="88e97-410">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="88e97-411">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="88e97-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="88e97-412">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="88e97-413">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="88e97-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="88e97-414">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="88e97-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="88e97-415">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="88e97-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="88e97-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="88e97-417">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="88e97-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="88e97-418">İçinde bir özel durum üretiliyor `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="88e97-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="88e97-419">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önler.</span><span class="sxs-lookup"><span data-stu-id="88e97-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="88e97-420">Başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="88e97-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt muhtemelen istemciye zaten gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="88e97-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="88e97-422">Yanıt istemciye zaten gönderildiyse, bu değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="88e97-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="88e97-423">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="88e97-424">`ResultExecutedContext.Exception` eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="88e97-425">`Exception`Null olarak ayarlanması bir özel durumu işler ve sonra işlem hattının daha sonra yeniden oluşturulmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="88e97-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="88e97-426">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="88e97-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="88e97-427">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="88e97-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="88e97-428">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="88e97-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="88e97-429">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="88e97-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="88e97-430">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="88e97-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="88e97-431">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="88e97-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="88e97-432">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="88e97-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="88e97-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="88e97-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="88e97-434">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="88e97-434">This includes action results produced by:</span></span>

* <span data-ttu-id="88e97-435">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="88e97-436">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-436">Exception filters.</span></span>

<span data-ttu-id="88e97-437">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="88e97-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="88e97-438">IFilterFactory</span></span>

<span data-ttu-id="88e97-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="88e97-440">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="88e97-441">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="88e97-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="88e97-442">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="88e97-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="88e97-443">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="88e97-444">`IFilterFactory` , filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="88e97-445">Filtre aşağıdaki kodda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="88e97-446">Önceki kodu [indirme örneğini](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)çalıştırarak test edin:</span><span class="sxs-lookup"><span data-stu-id="88e97-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="88e97-447">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="88e97-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="88e97-448">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="88e97-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="88e97-449">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="88e97-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="88e97-450">**Yazar:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="88e97-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="88e97-451">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="88e97-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="88e97-452">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="88e97-452">**internal:** `My header`</span></span>

<span data-ttu-id="88e97-453">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="88e97-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="88e97-454">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="88e97-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="88e97-455">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="88e97-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="88e97-456">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="88e97-457">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="88e97-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="88e97-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="88e97-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="88e97-459">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="88e97-460">`CreateInstance` belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="88e97-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="88e97-461">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="88e97-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="88e97-462">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="88e97-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="88e97-463">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="88e97-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="88e97-464">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="88e97-465">Ancak filtreler, çalışma zamanının bir parçası oldukları ve bu sayede bağlam ve yapılara erişimi olan ara yazılımlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="88e97-466">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="88e97-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="88e97-467">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="88e97-468"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="88e97-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="88e97-469">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="88e97-470">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="88e97-470">Next actions</span></span>

* <span data-ttu-id="88e97-471">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="88e97-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="88e97-472">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="88e97-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88e97-473">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="88e97-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="88e97-474">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="88e97-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="88e97-475">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="88e97-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="88e97-476">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="88e97-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="88e97-477">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="88e97-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="88e97-478">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="88e97-479">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="88e97-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="88e97-480">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88e97-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="88e97-481">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="88e97-482">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="88e97-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="88e97-483">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) .</span><span class="sxs-lookup"><span data-stu-id="88e97-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="88e97-484">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="88e97-484">How filters work</span></span>

<span data-ttu-id="88e97-485">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="88e97-486">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve ASP.NET Core eylemi çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="88e97-489">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="88e97-489">Filter types</span></span>

<span data-ttu-id="88e97-490">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="88e97-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="88e97-491">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="88e97-492">Yetkilendirme filtreleri, istek yetkisiz ise işlem hattı kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="88e97-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="88e97-493">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="88e97-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="88e97-494">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="88e97-494">Run after authorization.</span></span>  
  * <span data-ttu-id="88e97-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> Filtre işlem hattının geri kalanından önce kod çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="88e97-496">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="88e97-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="88e97-498">[Eylem filtreleri](#action-filters) , tek bir eylem yöntemi çağrıldıktan hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="88e97-499">Bir eyleme geçirilen bağımsız değişkenleri ve eylemden döndürülen sonucu işlemek için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="88e97-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="88e97-500">Eylem filtreleri sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="88e97-501">[Özel durum filtreleri](#exception-filters) , yanıt gövdesine hiçbir şey yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="88e97-502">[Sonuç filtreleri](#result-filters) , tek tek eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="88e97-503">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="88e97-504">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="88e97-505">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="88e97-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="88e97-508">Uygulama</span><span class="sxs-lookup"><span data-stu-id="88e97-508">Implementation</span></span>

<span data-ttu-id="88e97-509">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="88e97-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="88e97-510">Zaman uyumlu filtreler `On-Stage-Executing` `On-Stage-Executed` , ardışık düzen aşamasını () ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="88e97-511">Örneğin, `OnActionExecuting` eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="88e97-512">`OnActionExecuted` , eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="88e97-513">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="88e97-514">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="88e97-515">Yöntemlerin her biri, `On-Stage-ExecutionAsync` `FilterType-ExecutionDelegate` filtrenin ardışık düzen aşamasını yürüten bir alır.</span><span class="sxs-lookup"><span data-stu-id="88e97-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="88e97-516">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="88e97-516">Multiple filter stages</span></span>

<span data-ttu-id="88e97-517">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="88e97-518">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı `IActionFilter` , `IResultFilter` ve zaman uyumsuz eşdeğerlerini uygular.</span><span class="sxs-lookup"><span data-stu-id="88e97-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="88e97-519">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="88e97-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="88e97-520">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="88e97-521">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="88e97-522">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="88e97-523">Soyut sınıfların kullanıldığı durumlarda <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="88e97-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="88e97-524">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="88e97-524">Built-in filter attributes</span></span>

<span data-ttu-id="88e97-525">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="88e97-526">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="88e97-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-527">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="88e97-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="88e97-528">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="88e97-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="88e97-529">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88e97-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="88e97-530">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="88e97-531">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="88e97-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="88e97-532">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="88e97-533">Bir eylem üzerinde bir öznitelik kullanma.</span><span class="sxs-lookup"><span data-stu-id="88e97-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="88e97-534">Bir denetleyicide bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="88e97-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="88e97-535">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler ve eylemler için genel olarak:</span><span class="sxs-lookup"><span data-stu-id="88e97-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="88e97-536">Yukarıdaki kod, [Mvcoptions. Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) koleksiyonunu kullanarak genel olarak üç filtre ekler.</span><span class="sxs-lookup"><span data-stu-id="88e97-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="88e97-537">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="88e97-537">Default order of execution</span></span>

<span data-ttu-id="88e97-538">*Aynı türde*birden çok filtre olduğunda kapsam, filtre yürütmenin varsayılan sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="88e97-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="88e97-539">Genel filtreler saran sınıf filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-539">Global filters surround class filters.</span></span> <span data-ttu-id="88e97-540">Sınıf filtreleri surround yöntemi filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-540">Class filters surround method filters.</span></span>

<span data-ttu-id="88e97-541">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="88e97-542">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="88e97-542">The filter sequence:</span></span>

* <span data-ttu-id="88e97-543">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="88e97-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="88e97-544">Denetleyici filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="88e97-545">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="88e97-546">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="88e97-547">Denetleyici filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="88e97-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="88e97-548">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="88e97-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="88e97-549">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="88e97-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="88e97-550">Sequence</span><span class="sxs-lookup"><span data-stu-id="88e97-550">Sequence</span></span> | <span data-ttu-id="88e97-551">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="88e97-551">Filter scope</span></span> | <span data-ttu-id="88e97-552">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="88e97-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="88e97-553">1</span><span class="sxs-lookup"><span data-stu-id="88e97-553">1</span></span> | <span data-ttu-id="88e97-554">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-555">2</span><span class="sxs-lookup"><span data-stu-id="88e97-555">2</span></span> | <span data-ttu-id="88e97-556">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="88e97-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-557">3</span><span class="sxs-lookup"><span data-stu-id="88e97-557">3</span></span> | <span data-ttu-id="88e97-558">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-559">4</span><span class="sxs-lookup"><span data-stu-id="88e97-559">4</span></span> | <span data-ttu-id="88e97-560">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="88e97-561">5</span><span class="sxs-lookup"><span data-stu-id="88e97-561">5</span></span> | <span data-ttu-id="88e97-562">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="88e97-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="88e97-563">6</span><span class="sxs-lookup"><span data-stu-id="88e97-563">6</span></span> | <span data-ttu-id="88e97-564">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="88e97-565">Bu sıra şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="88e97-565">This sequence shows:</span></span>

* <span data-ttu-id="88e97-566">Yöntem filtresi, denetleyici filtresi içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="88e97-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="88e97-567">Denetleyici filtresi, genel filtrenin içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="88e97-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="88e97-568">Denetleyici ve Razor sayfa düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="88e97-569">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="88e97-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="88e97-570">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="88e97-570">These methods:</span></span>

* <span data-ttu-id="88e97-571">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="88e97-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="88e97-572">`OnActionExecuting` , eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="88e97-573">`OnActionExecuted` tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="88e97-574">`OnActionExecutionAsync` , eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="88e97-575">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="88e97-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="88e97-576">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="88e97-577">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="88e97-577">The `TestController`:</span></span>

* <span data-ttu-id="88e97-578">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="88e97-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="88e97-579">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="88e97-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="88e97-580">' A gitme `https://localhost:5001/Test/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="88e97-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="88e97-581">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="88e97-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="88e97-582">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="88e97-582">Overriding the default order</span></span>

<span data-ttu-id="88e97-583">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="88e97-584">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="88e97-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="88e97-585">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="88e97-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="88e97-586">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="88e97-587">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="88e97-588">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="88e97-589">Yukarıdaki örnekte gösterilen 3 eylem filtresini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="88e97-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="88e97-590">`Order`Denetleyicinin ve genel filtrelerin özelliği sırasıyla 1 ve 2 olarak ayarlandıysa, yürütme sırası tersine çevrilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="88e97-591">Sequence</span><span class="sxs-lookup"><span data-stu-id="88e97-591">Sequence</span></span> | <span data-ttu-id="88e97-592">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="88e97-592">Filter scope</span></span> | <span data-ttu-id="88e97-593">`Order` özelliði</span><span class="sxs-lookup"><span data-stu-id="88e97-593">`Order` property</span></span> | <span data-ttu-id="88e97-594">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="88e97-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="88e97-595">1</span><span class="sxs-lookup"><span data-stu-id="88e97-595">1</span></span> | <span data-ttu-id="88e97-596">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-596">Method</span></span> | <span data-ttu-id="88e97-597">0</span><span class="sxs-lookup"><span data-stu-id="88e97-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="88e97-598">2</span><span class="sxs-lookup"><span data-stu-id="88e97-598">2</span></span> | <span data-ttu-id="88e97-599">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="88e97-599">Controller</span></span> | <span data-ttu-id="88e97-600">1</span><span class="sxs-lookup"><span data-stu-id="88e97-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="88e97-601">3</span><span class="sxs-lookup"><span data-stu-id="88e97-601">3</span></span> | <span data-ttu-id="88e97-602">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-602">Global</span></span> | <span data-ttu-id="88e97-603">2</span><span class="sxs-lookup"><span data-stu-id="88e97-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="88e97-604">4</span><span class="sxs-lookup"><span data-stu-id="88e97-604">4</span></span> | <span data-ttu-id="88e97-605">Genel</span><span class="sxs-lookup"><span data-stu-id="88e97-605">Global</span></span> | <span data-ttu-id="88e97-606">2</span><span class="sxs-lookup"><span data-stu-id="88e97-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="88e97-607">5</span><span class="sxs-lookup"><span data-stu-id="88e97-607">5</span></span> | <span data-ttu-id="88e97-608">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="88e97-608">Controller</span></span> | <span data-ttu-id="88e97-609">1</span><span class="sxs-lookup"><span data-stu-id="88e97-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="88e97-610">6</span><span class="sxs-lookup"><span data-stu-id="88e97-610">6</span></span> | <span data-ttu-id="88e97-611">Yöntem</span><span class="sxs-lookup"><span data-stu-id="88e97-611">Method</span></span> | <span data-ttu-id="88e97-612">0</span><span class="sxs-lookup"><span data-stu-id="88e97-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="88e97-613">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="88e97-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="88e97-614">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="88e97-615">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="88e97-616">Yerleşik filtreler için kapsam, `Order` sıfır olmayan bir değere ayarlanmadığı takdirde sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="88e97-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="88e97-617">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="88e97-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="88e97-618">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="88e97-619">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="88e97-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-620">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="88e97-621">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="88e97-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="88e97-622">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="88e97-623">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="88e97-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="88e97-624">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="88e97-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="88e97-625">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="88e97-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="88e97-626">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="88e97-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="88e97-627">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="88e97-627">Dependency injection</span></span>

<span data-ttu-id="88e97-628">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="88e97-629">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="88e97-630">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="88e97-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="88e97-631">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="88e97-631">A type-activated filter means:</span></span>

* <span data-ttu-id="88e97-632">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88e97-632">An instance is created for each request.</span></span>
* <span data-ttu-id="88e97-633">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="88e97-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="88e97-634">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="88e97-635">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="88e97-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="88e97-636">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="88e97-637">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="88e97-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="88e97-638">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="88e97-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="88e97-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="88e97-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="88e97-640">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="88e97-641">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-641">Loggers are available from DI.</span></span> <span data-ttu-id="88e97-642">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="88e97-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="88e97-643">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="88e97-644">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="88e97-644">Logging added to filters:</span></span>

* <span data-ttu-id="88e97-645">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="88e97-646">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="88e97-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="88e97-647">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="88e97-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="88e97-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="88e97-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="88e97-649">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88e97-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="88e97-650">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="88e97-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="88e97-651">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="88e97-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="88e97-652">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="88e97-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="88e97-653">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="88e97-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="88e97-654">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="88e97-655">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="88e97-656">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="88e97-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="88e97-657">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88e97-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="88e97-658">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="88e97-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="88e97-659">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="88e97-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="88e97-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="88e97-661">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="88e97-662">`CreateInstance` belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="88e97-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="88e97-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="88e97-663">TypeFilterAttribute</span></span>

<span data-ttu-id="88e97-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="88e97-665">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="88e97-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="88e97-666">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="88e97-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="88e97-667">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="88e97-668">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="88e97-669">`TypeFilterAttribute` isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="88e97-670">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="88e97-671">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="88e97-672">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="88e97-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="88e97-673">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="88e97-674">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="88e97-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="88e97-675">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-675">Authorization filters</span></span>

<span data-ttu-id="88e97-676">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-676">Authorization filters:</span></span>

* <span data-ttu-id="88e97-677">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="88e97-678">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-678">Control access to action methods.</span></span>
* <span data-ttu-id="88e97-679">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="88e97-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="88e97-680">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="88e97-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="88e97-681">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="88e97-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="88e97-682">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="88e97-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="88e97-683">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="88e97-683">Calls the authorization system.</span></span>
* <span data-ttu-id="88e97-684">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-684">Does not authorize requests.</span></span>

<span data-ttu-id="88e97-685">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="88e97-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="88e97-686">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="88e97-686">The exception will not be handled.</span></span>
* <span data-ttu-id="88e97-687">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="88e97-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="88e97-688">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="88e97-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="88e97-689">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="88e97-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="88e97-690">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-690">Resource filters</span></span>

<span data-ttu-id="88e97-691">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-691">Resource filters:</span></span>

* <span data-ttu-id="88e97-692"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="88e97-693">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="88e97-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="88e97-694">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="88e97-695">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="88e97-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="88e97-696">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="88e97-697">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-697">Resource filter examples:</span></span>

* <span data-ttu-id="88e97-698">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="88e97-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="88e97-699">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="88e97-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="88e97-700">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="88e97-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="88e97-701">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88e97-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="88e97-702">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="88e97-703">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="88e97-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="88e97-704">Razor Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="88e97-705">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="88e97-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="88e97-706">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-706">Action filters:</span></span>

* <span data-ttu-id="88e97-707"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="88e97-708">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="88e97-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="88e97-709">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="88e97-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="88e97-710">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="88e97-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -bir eylem yöntemine yönelik girişlerin okunmalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="88e97-712"><xref:Microsoft.AspNetCore.Mvc.Controller> -denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="88e97-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="88e97-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="88e97-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="88e97-714">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="88e97-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="88e97-715">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="88e97-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="88e97-716">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="88e97-717">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="88e97-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> -Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="88e97-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="88e97-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> -Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="88e97-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="88e97-720">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-720">Setting this property to null:</span></span>

  * <span data-ttu-id="88e97-721">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="88e97-722">`Result` eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="88e97-723">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="88e97-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="88e97-724">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="88e97-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="88e97-725">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="88e97-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="88e97-726">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="88e97-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="88e97-727">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="88e97-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="88e97-728">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="88e97-729">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88e97-729">Validate model state.</span></span>
* <span data-ttu-id="88e97-730">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="88e97-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="88e97-731">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="88e97-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="88e97-732">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="88e97-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="88e97-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="88e97-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="88e97-735">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="88e97-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="88e97-736">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="88e97-737">`ActionExecutedContext.Result` eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="88e97-738">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-738">Exception filters</span></span>

<span data-ttu-id="88e97-739">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-739">Exception filters:</span></span>

* <span data-ttu-id="88e97-740"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="88e97-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="88e97-741">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="88e97-742">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="88e97-743">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-743">Exception filters:</span></span>

* <span data-ttu-id="88e97-744">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="88e97-744">Don't have before and after events.</span></span>
* <span data-ttu-id="88e97-745"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="88e97-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="88e97-746">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="88e97-747">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="88e97-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="88e97-748">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="88e97-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="88e97-749">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="88e97-749">This stops propagation of the exception.</span></span> <span data-ttu-id="88e97-750">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="88e97-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="88e97-751">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-751">Only an action filter can do that.</span></span>

<span data-ttu-id="88e97-752">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-752">Exception filters:</span></span>

* <span data-ttu-id="88e97-753">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="88e97-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="88e97-754">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="88e97-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="88e97-755">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="88e97-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="88e97-756">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="88e97-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="88e97-757">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="88e97-758">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="88e97-759">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="88e97-759">Result filters</span></span>

<span data-ttu-id="88e97-760">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="88e97-760">Result filters:</span></span>

* <span data-ttu-id="88e97-761">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="88e97-761">Implement an interface:</span></span>
  * <span data-ttu-id="88e97-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="88e97-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="88e97-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="88e97-764">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="88e97-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="88e97-765">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="88e97-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="88e97-766">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="88e97-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="88e97-767">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="88e97-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="88e97-768">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="88e97-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="88e97-769">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="88e97-770">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="88e97-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="88e97-771">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88e97-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="88e97-772">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="88e97-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="88e97-773">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="88e97-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="88e97-774">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="88e97-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="88e97-775"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="88e97-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="88e97-776">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="88e97-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="88e97-777">Bir özel durum oluşturmak için `IResultFilter.OnResultExecuting` şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="88e97-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="88e97-778">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="88e97-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="88e97-779">Başarılı bir sonuç yerine hata olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="88e97-780"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt zaten istemciye gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="88e97-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="88e97-781">Yanıt istemciye zaten gönderildiyse, daha fazla değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="88e97-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="88e97-782">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="88e97-783">`ResultExecutedContext.Exception` eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="88e97-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="88e97-784">`Exception`Null olarak ayarlanması bir özel durumu işler ve işlem hattının ilerleyen kısımlarında ASP.NET Core özel durumun yeniden oluşturulmasını önler.</span><span class="sxs-lookup"><span data-stu-id="88e97-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="88e97-785">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="88e97-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="88e97-786">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="88e97-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="88e97-787">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="88e97-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="88e97-788">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="88e97-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="88e97-789">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="88e97-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="88e97-790">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="88e97-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="88e97-791">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="88e97-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="88e97-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="88e97-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="88e97-793">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="88e97-793">This includes action results produced by:</span></span>

* <span data-ttu-id="88e97-794">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="88e97-795">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="88e97-795">Exception filters.</span></span>

<span data-ttu-id="88e97-796">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="88e97-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="88e97-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="88e97-797">IFilterFactory</span></span>

<span data-ttu-id="88e97-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="88e97-799">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88e97-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="88e97-800">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="88e97-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="88e97-801">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="88e97-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="88e97-802">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="88e97-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="88e97-803">`IFilterFactory` , filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="88e97-804">Önceki kod, [indirme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)çalıştırılarak test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="88e97-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="88e97-805">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="88e97-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="88e97-806">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="88e97-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="88e97-807">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="88e97-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="88e97-808">**Yazar:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="88e97-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="88e97-809">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="88e97-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="88e97-810">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="88e97-810">**internal:** `My header`</span></span>

<span data-ttu-id="88e97-811">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="88e97-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="88e97-812">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="88e97-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="88e97-813">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="88e97-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="88e97-814">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88e97-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="88e97-815">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="88e97-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="88e97-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="88e97-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="88e97-817">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="88e97-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="88e97-818">`CreateInstance` belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="88e97-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="88e97-819">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="88e97-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="88e97-820">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="88e97-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="88e97-821">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="88e97-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="88e97-822">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="88e97-823">Ancak filtreler, ASP.NET Core çalışma zamanının bir parçası olan ara yazılımlar dışında farklılık gösterir, bu da ASP.NET Core bağlamına ve yapılarına erişime sahip oldukları anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="88e97-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="88e97-824">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="88e97-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="88e97-825">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="88e97-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="88e97-826"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="88e97-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="88e97-827">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="88e97-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="88e97-828">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="88e97-828">Next actions</span></span>

* <span data-ttu-id="88e97-829">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="88e97-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="88e97-830">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="88e97-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
