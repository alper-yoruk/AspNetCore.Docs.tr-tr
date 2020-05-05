---
title: ASP.NET Core filtreler
author: Rick-Anderson
description: Filtrelerin nasıl çalıştığını ve ASP.NET Core nasıl kullanılacağını öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776792"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="7f17c-103">ASP.NET Core filtreler</span><span class="sxs-lookup"><span data-stu-id="7f17c-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f17c-104">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="7f17c-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7f17c-105">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7f17c-106">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7f17c-107">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="7f17c-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7f17c-108">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="7f17c-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7f17c-109">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7f17c-110">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7f17c-111">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7f17c-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="7f17c-112">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7f17c-113">Bu belge, görünümler içeren Razor Pages, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="7f17c-114">Filtreler, [Razor bileşenleriyle](xref:blazor/components)doğrudan çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="7f17c-115">Filtre, şu durumlarda bir bileşeni yalnızca dolaylı olarak etkileyebilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="7f17c-116">Bileşen bir sayfa veya görünüme katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="7f17c-117">Sayfa veya denetleyici/görünüm filtreyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="7f17c-118">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) .</span><span class="sxs-lookup"><span data-stu-id="7f17c-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7f17c-119">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="7f17c-119">How filters work</span></span>

<span data-ttu-id="7f17c-120">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="7f17c-121">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve eylem çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7f17c-124">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-124">Filter types</span></span>

<span data-ttu-id="7f17c-125">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="7f17c-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7f17c-126">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7f17c-127">Yetkilendirme filtreleri, istek yetkilendirilmezse işlem hattı kısa devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="7f17c-128">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7f17c-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7f17c-129">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-129">Run after authorization.</span></span>  
  * <span data-ttu-id="7f17c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanında kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7f17c-131">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="7f17c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7f17c-133">[Eylem filtreleri](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="7f17c-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="7f17c-134">Kodu bir eylem yöntemi çağrıldıktan hemen önce ve sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="7f17c-135">Bir eyleme geçirilen bağımsız değişkenleri değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="7f17c-136">Eylemden döndürülen sonucu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="7f17c-137">Razor Pages **desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7f17c-138">[Özel durum filtreleri](#exception-filters) , yanıt gövdesinin üzerine yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygular.</span><span class="sxs-lookup"><span data-stu-id="7f17c-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="7f17c-139">[Sonuç filtreleri](#result-filters) , eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="7f17c-140">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7f17c-141">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7f17c-142">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7f17c-145">Uygulama</span><span class="sxs-lookup"><span data-stu-id="7f17c-145">Implementation</span></span>

<span data-ttu-id="7f17c-146">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7f17c-147">Zaman uyumlu filtreler, ardışık düzen aşamasından önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="7f17c-148">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="7f17c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7f17c-150">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="7f17c-151">Örneğin <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="7f17c-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7f17c-152">Önceki kodda,, `SampleAsyncActionFilter` Action metodunu yürüten bir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7f17c-153">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="7f17c-153">Multiple filter stages</span></span>

<span data-ttu-id="7f17c-154">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7f17c-155">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="7f17c-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="7f17c-156">Zaman uyumlu <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> : ve<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="7f17c-157">Zaman uyumsuz <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> : ve<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="7f17c-158">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7f17c-159">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7f17c-160">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7f17c-161">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7f17c-162">Gibi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>soyut sınıflar kullanırken, her bir filtre türü için yalnızca zaman uyumlu yöntemleri veya zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7f17c-163">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-163">Built-in filter attributes</span></span>

<span data-ttu-id="7f17c-164">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7f17c-165">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-166">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7f17c-167">`AddHeaderAttribute` ' İ bir denetleyiciye veya eylem yöntemine UYGULAYıN ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="7f17c-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="7f17c-168">Üst bilgileri incelemek için [tarayıcı geliştirici araçları](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="7f17c-169">**Yanıt üst bilgileri** `author: Rick Anderson` altında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="7f17c-170">Aşağıdaki kod şunları uygular `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7f17c-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="7f17c-171">Yapılandırma sistemindeki başlığı ve adı okur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="7f17c-172">Önceki örnekten farklı olarak, aşağıdaki kod koda filtre parametrelerinin eklenmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="7f17c-173">Başlık ve adı yanıt üstbilgisine ekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-174">Yapılandırma seçenekleri, [Seçenekler deseninin](xref:fundamentals/configuration/options)kullanıldığı [yapılandırma sisteminden](xref:fundamentals/configuration/index) sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7f17c-175">Örneğin, *appSettings. JSON* dosyasından:</span><span class="sxs-lookup"><span data-stu-id="7f17c-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="7f17c-176">İçinde `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7f17c-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="7f17c-177">`PositionOptions` Sınıfı, `"Position"` yapılandırma alanı ile hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="7f17c-178">, `MyActionFilterAttribute` Hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="7f17c-179">Aşağıdaki kod, `PositionOptions` sınıfını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="7f17c-180">Aşağıdaki kod `MyActionFilterAttribute` `Index2` yöntemi için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="7f17c-181">**Yanıt üst bilgileri**altında `author: Rick Anderson`, ve `Editor: Joe Smith` `Sample/Index2` uç nokta çağrıldığında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="7f17c-182">Aşağıdaki kod, `MyActionFilterAttribute` ve ' i `AddHeaderAttribute` Razor sayfasına uygular:</span><span class="sxs-lookup"><span data-stu-id="7f17c-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7f17c-183">, Razor sayfası işleyici yöntemlerine filtre uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="7f17c-184">Bu kişiler Razor sayfa modeline veya küresel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="7f17c-185">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7f17c-186">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7f17c-187">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="7f17c-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="7f17c-188">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7f17c-189">Bir denetleyici eyleminde bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="7f17c-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="7f17c-190">Filtre öznitelikleri Razor Pages işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="7f17c-191">Bir denetleyici veya Razor sayfasında bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="7f17c-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="7f17c-192">Genel olarak tüm denetleyiciler, Eylemler ve Razor Pages aşağıdaki kodda gösterildiği gibi:</span><span class="sxs-lookup"><span data-stu-id="7f17c-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="7f17c-193">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="7f17c-193">Default order of execution</span></span>

<span data-ttu-id="7f17c-194">İşlem hattının belirli bir aşamasına ilişkin birden çok filtre olduğunda, kapsam varsayılan filtre yürütme sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7f17c-195">Genel filtreler kapsayan sınıf filtreleri, bu da kapsayan Yöntem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="7f17c-196">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7f17c-197">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="7f17c-197">The filter sequence:</span></span>

* <span data-ttu-id="7f17c-198">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7f17c-199">Denetleyicinin ve Razor sayfası filtrelerinin *öncesindeki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="7f17c-200">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7f17c-201">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7f17c-202">Denetleyicinin ve Razor sayfası filtrelerinin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="7f17c-203">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7f17c-204">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="7f17c-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7f17c-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="7f17c-205">Sequence</span></span> | <span data-ttu-id="7f17c-206">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7f17c-206">Filter scope</span></span> | <span data-ttu-id="7f17c-207">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7f17c-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7f17c-208">1</span><span class="sxs-lookup"><span data-stu-id="7f17c-208">1</span></span> | <span data-ttu-id="7f17c-209">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-210">2</span><span class="sxs-lookup"><span data-stu-id="7f17c-210">2</span></span> | <span data-ttu-id="7f17c-211">Denetleyici veya Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="7f17c-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="7f17c-212">3</span><span class="sxs-lookup"><span data-stu-id="7f17c-212">3</span></span> | <span data-ttu-id="7f17c-213">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-214">4</span><span class="sxs-lookup"><span data-stu-id="7f17c-214">4</span></span> | <span data-ttu-id="7f17c-215">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7f17c-216">5</span><span class="sxs-lookup"><span data-stu-id="7f17c-216">5</span></span> | <span data-ttu-id="7f17c-217">Denetleyici veya Razor sayfası</span><span class="sxs-lookup"><span data-stu-id="7f17c-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7f17c-218">6</span><span class="sxs-lookup"><span data-stu-id="7f17c-218">6</span></span> | <span data-ttu-id="7f17c-219">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="7f17c-220">Denetleyici düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-220">Controller level filters</span></span>

<span data-ttu-id="7f17c-221"><xref:Microsoft.AspNetCore.Mvc.Controller> Temel sınıftan devralan her denetleyici [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7f17c-222">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-222">These methods:</span></span>

* <span data-ttu-id="7f17c-223">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7f17c-224">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7f17c-225">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7f17c-226">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7f17c-227">Eylem yöntemiyle çalıştıktan sonra `next` filtredeki kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7f17c-228">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7f17c-229">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-229">The `TestController`:</span></span>

* <span data-ttu-id="7f17c-230">`FilterTest2` Eyleme `SampleActionFilterAttribute` (`[SampleActionFilter]`) uygular.</span><span class="sxs-lookup"><span data-stu-id="7f17c-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7f17c-231">Geçersiz `OnActionExecuting` kılmalar `OnActionExecuted`ve.</span><span class="sxs-lookup"><span data-stu-id="7f17c-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="7f17c-232">' A `https://localhost:5001/Test2/FilterTest2` gitme aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7f17c-233">Denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini olarak `int.MinValue`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="7f17c-234">Denetleyici düzeyi filtreleri metotlara uygulandıktan sonra çalıştırılacak şekilde ayarlanamaz. **not**</span><span class="sxs-lookup"><span data-stu-id="7f17c-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="7f17c-235">Sıra, sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-235">Order is explained in the next section.</span></span>

<span data-ttu-id="7f17c-236">Razor Pages için bkz. [filtre yöntemlerini geçersiz kılarak Razor sayfası filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7f17c-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7f17c-237">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="7f17c-237">Overriding the default order</span></span>

<span data-ttu-id="7f17c-238">Varsayılan yürütme sırası, uygulama <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7f17c-239">`IOrderedFilter`yürütme sırasını <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7f17c-240">Düşük `Order` değere sahip bir filtre:</span><span class="sxs-lookup"><span data-stu-id="7f17c-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7f17c-241">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order`.</span><span class="sxs-lookup"><span data-stu-id="7f17c-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7f17c-242">Daha yüksek *after* `Order` bir değere sahip bir filtrenin sonrasında koddan sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7f17c-243">`Order` Özelliği bir Oluşturucu parametresiyle ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="7f17c-244">Aşağıdaki denetleyicide iki eylem filtresini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="7f17c-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7f17c-245">İçinde `StartUp.ConfigureServices`genel bir filtre eklenir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="7f17c-246">3 filtre aşağıdaki sırayla çalışır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="7f17c-247">`Order` Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7f17c-248">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7f17c-249">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7f17c-250">Daha önce belirtildiği gibi, denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini yerleşik filtreler `int.MinValue` için olarak ayarlar, sıfır olmayan bir değere ayarlanmadığı sürece `Order` kapsam sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="7f17c-251">Önceki kodda, `MySampleActionFilter` denetleyici kapsamı olan, daha önce `MyAction2FilterAttribute`çalışması için genel kapsama sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="7f17c-252">Önce `MyAction2FilterAttribute` çalıştırmak için, sırayı şu şekilde `int.MinValue`ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="7f17c-253">Önce genel filtrenin `MySampleActionFilter` çalışmasını sağlamak için şu şekilde `Order` `int.MinValue`ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7f17c-254">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="7f17c-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7f17c-255">Filtre işlem hattı, filtre yöntemine sunulan <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7f17c-256">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="7f17c-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-257">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7f17c-258">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7f17c-259">İlk olarak bir kaynak filtresi olduğundan ve `AddHeader` bir eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7f17c-260">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7f17c-261">Bu nedenle `AddHeader` , filtre `SomeResource` eylem için hiçbir şekilde çalışmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7f17c-262">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak `ShortCircuitingResourceFilter` çalıştırıldığında aynı olur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7f17c-263">İlk `ShortCircuitingResourceFilter` olarak, filtre türü veya açıkça `Order` özelliğin kullanımı kullanılarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="7f17c-264">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7f17c-264">Dependency injection</span></span>

<span data-ttu-id="7f17c-265">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7f17c-266">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7f17c-267">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7f17c-268">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-268">A type-activated filter means:</span></span>

* <span data-ttu-id="7f17c-269">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-269">An instance is created for each request.</span></span>
* <span data-ttu-id="7f17c-270">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7f17c-271">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7f17c-272">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="7f17c-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7f17c-273">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7f17c-274">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7f17c-275">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7f17c-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7f17c-277">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7f17c-278">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-278">Loggers are available from DI.</span></span> <span data-ttu-id="7f17c-279">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7f17c-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7f17c-280">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7f17c-281">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="7f17c-281">Logging added to filters:</span></span>

* <span data-ttu-id="7f17c-282">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7f17c-283">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="7f17c-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7f17c-284">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="7f17c-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7f17c-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7f17c-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="7f17c-286">Hizmet filtresi uygulama türleri ' de `ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7f17c-287">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7f17c-288">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7f17c-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7f17c-289">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="7f17c-290">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir `AddHeaderResultServiceFilter` filtrenin bir örneğini dı:</span><span class="sxs-lookup"><span data-stu-id="7f17c-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7f17c-291">Kullanırken `ServiceFilterAttribute`, [servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7f17c-292">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7f17c-293">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="7f17c-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7f17c-294">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7f17c-295">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="7f17c-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7f17c-296">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7f17c-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7f17c-298">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7f17c-299">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7f17c-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7f17c-300">TypeFilterAttribute</span></span>

<span data-ttu-id="7f17c-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7f17c-302">Kullanarak <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>türü başlatır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7f17c-303">Türler `TypeFilterAttribute` doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="7f17c-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7f17c-304">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7f17c-305">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7f17c-306">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7f17c-307">Kullanırken `TypeFilterAttribute`, [typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7f17c-308">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7f17c-309">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7f17c-310">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7f17c-311">Aşağıdaki örnek kullanarak `TypeFilterAttribute`bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7f17c-312">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-312">Authorization filters</span></span>

<span data-ttu-id="7f17c-313">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-313">Authorization filters:</span></span>

* <span data-ttu-id="7f17c-314">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7f17c-315">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-315">Control access to action methods.</span></span>
* <span data-ttu-id="7f17c-316">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="7f17c-317">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7f17c-318">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7f17c-319">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="7f17c-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="7f17c-320">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-320">Calls the authorization system.</span></span>
* <span data-ttu-id="7f17c-321">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-321">Does not authorize requests.</span></span>

<span data-ttu-id="7f17c-322">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="7f17c-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7f17c-323">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-323">The exception will not be handled.</span></span>
* <span data-ttu-id="7f17c-324">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7f17c-325">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="7f17c-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7f17c-326">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7f17c-327">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-327">Resource filters</span></span>

<span data-ttu-id="7f17c-328">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-328">Resource filters:</span></span>

* <span data-ttu-id="7f17c-329"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> da arabirimini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7f17c-330">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7f17c-331">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7f17c-332">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7f17c-333">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7f17c-334">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-334">Resource filter examples:</span></span>

* <span data-ttu-id="7f17c-335">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="7f17c-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7f17c-336">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7f17c-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7f17c-337">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="7f17c-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7f17c-338">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7f17c-339">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-339">Action filters</span></span>

<span data-ttu-id="7f17c-340">Eylem filtreleri Razor Pages **için uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="7f17c-341">Razor Pages ve <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> destekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7f17c-342">Daha fazla bilgi için bkz. [Razor Pages Için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7f17c-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7f17c-343">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-343">Action filters:</span></span>

* <span data-ttu-id="7f17c-344"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> da arabirimini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7f17c-345">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7f17c-346">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7f17c-347">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7f17c-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-girişlerin bir eylem yöntemine okunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="7f17c-349"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7f17c-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Eylem `Result` yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="7f17c-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7f17c-351">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="7f17c-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7f17c-352">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7f17c-353">Ayarından `Result`farklı olarak, başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7f17c-354">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Ve `Controller` `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7f17c-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="7f17c-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7f17c-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="7f17c-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7f17c-357">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-357">Setting this property to null:</span></span>

  * <span data-ttu-id="7f17c-358">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7f17c-359">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7f17c-360">Bir `IAsyncActionFilter`için bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="7f17c-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7f17c-361">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7f17c-362">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7f17c-363">Kısa devre dışı, bir sonuç <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="7f17c-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7f17c-364">Çerçeve, alt sınıflı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> olabilecek bir Özet sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7f17c-365">`OnActionExecuting` Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7f17c-366">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-366">Validate model state.</span></span>
* <span data-ttu-id="7f17c-367">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-368">`OnActionExecuted` Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7f17c-369">Ve, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7f17c-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7f17c-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7f17c-372">Null `Exception` olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7f17c-373">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7f17c-374">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7f17c-375">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-375">Exception filters</span></span>

<span data-ttu-id="7f17c-376">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-376">Exception filters:</span></span>

* <span data-ttu-id="7f17c-377">Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="7f17c-378">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7f17c-379">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7f17c-380">Aşağıdaki kod özel durum filtresini sınar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="7f17c-381">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-381">Exception filters:</span></span>

* <span data-ttu-id="7f17c-382">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="7f17c-382">Don't have before and after events.</span></span>
* <span data-ttu-id="7f17c-383">Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7f17c-384">Razor sayfası veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7f17c-385">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="7f17c-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7f17c-386">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini bir yanıt `true` olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7f17c-387">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7f17c-387">This stops propagation of the exception.</span></span> <span data-ttu-id="7f17c-388">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7f17c-389">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-389">Only an action filter can do that.</span></span>

<span data-ttu-id="7f17c-390">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-390">Exception filters:</span></span>

* <span data-ttu-id="7f17c-391">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7f17c-392">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7f17c-393">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7f17c-394">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7f17c-395">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7f17c-396">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7f17c-397">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-397">Result filters</span></span>

<span data-ttu-id="7f17c-398">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-398">Result filters:</span></span>

* <span data-ttu-id="7f17c-399">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="7f17c-399">Implement an interface:</span></span>
  * <span data-ttu-id="7f17c-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7f17c-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7f17c-402">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7f17c-403">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="7f17c-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7f17c-404">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7f17c-405">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7f17c-406">Bir görünüm döndüren bir eylem, <xref:Microsoft.AspNetCore.Mvc.ViewResult> çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7f17c-407">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7f17c-408">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7f17c-409">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7f17c-410">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="7f17c-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="7f17c-411">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7f17c-412">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7f17c-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Yöntemi, olarak <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true`ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7f17c-414">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7f17c-415">İçinde `IResultFilter.OnResultExecuting`bir özel durum üretiliyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="7f17c-416">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7f17c-417">Başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7f17c-418"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> Yöntem çalıştığında, yanıt muhtemelen istemciye zaten gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="7f17c-419">Yanıt istemciye zaten gönderildiyse, bu değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="7f17c-420">`ResultExecutedContext.Canceled`eylem sonucu yürütmesi `true` başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7f17c-421">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7f17c-422">Null `Exception` olarak ayarlanması bir özel durumu işler ve sonra işlem hattının daha sonra yeniden oluşturulmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7f17c-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="7f17c-423">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7f17c-424">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7f17c-425">Bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>için bir çağrısı `await next` , sonraki sonuç filtrelerini <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7f17c-426">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="7f17c-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7f17c-427">Çerçeve, alt sınıflı `ResultFilterAttribute` olabilecek bir Özet sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7f17c-428">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7f17c-429">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="7f17c-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7f17c-430">Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, tüm eylem <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7f17c-431">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-431">This includes action results produced by:</span></span>

* <span data-ttu-id="7f17c-432">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7f17c-433">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-433">Exception filters.</span></span>

<span data-ttu-id="7f17c-434">Örneğin, aşağıdaki filtre her zaman çalışır ve içerik anlaşması başarısız olduğunda<xref:Microsoft.AspNetCore.Mvc.ObjectResult> *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7f17c-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7f17c-435">IFilterFactory</span></span>

<span data-ttu-id="7f17c-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7f17c-437">Bu nedenle, `IFilterFactory` bir örnek, filtre ardışık düzeninde `IFilterMetadata` herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7f17c-438">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir `IFilterFactory`öğesine dönüştürmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="7f17c-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7f17c-439">Atama başarılı olursa, çağrılan <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> `IFilterMetadata` örneği oluşturmak için yöntemi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7f17c-440">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7f17c-441">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7f17c-442">Filtre aşağıdaki kodda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="7f17c-443">Önceki kodu [indirme örneğini](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)çalıştırarak test edin:</span><span class="sxs-lookup"><span data-stu-id="7f17c-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="7f17c-444">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7f17c-445">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7f17c-446">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7f17c-447">**Yazar:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="7f17c-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="7f17c-448">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7f17c-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7f17c-449">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7f17c-449">**internal:** `My header`</span></span>

<span data-ttu-id="7f17c-450">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7f17c-451">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="7f17c-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7f17c-452">Uygulayan `IFilterFactory` filtreler şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7f17c-453">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="7f17c-454">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7f17c-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7f17c-456">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7f17c-457">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7f17c-458">Aşağıdaki kod, `[SampleActionFilter]`uygulamak için üç yaklaşımda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7f17c-459">Yukarıdaki kodda, yöntemi ile `[SampleActionFilter]` dekorasyon, uygulamak için tercih edilen yaklaşımdır. `SampleActionFilter`</span><span class="sxs-lookup"><span data-stu-id="7f17c-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7f17c-460">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="7f17c-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7f17c-461">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7f17c-462">Ancak filtreler, çalışma zamanının bir parçası oldukları ve bu sayede bağlam ve yapılara erişimi olan ara yazılımlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="7f17c-463">Ara yazılımı bir filtre olarak kullanmak için, filtre ardışık düzenine eklenecek `Configure` olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7f17c-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7f17c-464">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7f17c-465">Ara yazılımını <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7f17c-466">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7f17c-467">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="7f17c-467">Next actions</span></span>

* <span data-ttu-id="7f17c-468">[Razor Pages Için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7f17c-469">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="7f17c-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f17c-470">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="7f17c-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7f17c-471">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7f17c-472">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7f17c-473">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="7f17c-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7f17c-474">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="7f17c-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7f17c-475">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7f17c-476">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7f17c-477">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7f17c-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="7f17c-478">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7f17c-479">Bu belge, görünümler içeren Razor Pages, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="7f17c-480">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) .</span><span class="sxs-lookup"><span data-stu-id="7f17c-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7f17c-481">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="7f17c-481">How filters work</span></span>

<span data-ttu-id="7f17c-482">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="7f17c-483">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve ASP.NET Core eylemi çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7f17c-486">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-486">Filter types</span></span>

<span data-ttu-id="7f17c-487">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="7f17c-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7f17c-488">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7f17c-489">Yetkilendirme filtreleri, istek yetkisiz ise işlem hattı kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="7f17c-490">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7f17c-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7f17c-491">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-491">Run after authorization.</span></span>  
  * <span data-ttu-id="7f17c-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanından önce kod çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7f17c-493">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="7f17c-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7f17c-495">[Eylem filtreleri](#action-filters) , tek bir eylem yöntemi çağrıldıktan hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="7f17c-496">Bir eyleme geçirilen bağımsız değişkenleri ve eylemden döndürülen sonucu işlemek için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="7f17c-497">Eylem filtreleri Razor Pages **desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7f17c-498">[Özel durum filtreleri](#exception-filters) , yanıt gövdesine hiçbir şey yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="7f17c-499">[Sonuç filtreleri](#result-filters) , tek tek eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="7f17c-500">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7f17c-501">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7f17c-502">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7f17c-505">Uygulama</span><span class="sxs-lookup"><span data-stu-id="7f17c-505">Implementation</span></span>

<span data-ttu-id="7f17c-506">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7f17c-507">Zaman uyumlu filtreler, ardışık düzen aşamasını`On-Stage-Executing`() ve sonra`On-Stage-Executed`kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="7f17c-508">Örneğin, `OnActionExecuting` eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="7f17c-509">`OnActionExecuted`, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7f17c-510">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7f17c-511">Önceki kodda,, `SampleAsyncActionFilter` Action metodunu yürüten bir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="7f17c-512">`On-Stage-ExecutionAsync` Yöntemlerin her biri, filtrenin ardışık `FilterType-ExecutionDelegate` düzen aşamasını yürüten bir alır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7f17c-513">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="7f17c-513">Multiple filter stages</span></span>

<span data-ttu-id="7f17c-514">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7f17c-515">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı, `IActionFilter` `IResultFilter`ve zaman uyumsuz eşdeğerlerini uygular.</span><span class="sxs-lookup"><span data-stu-id="7f17c-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="7f17c-516">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7f17c-517">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7f17c-518">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7f17c-519">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7f17c-520">Soyut sınıfların kullanıldığı durumlarda, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7f17c-521">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-521">Built-in filter attributes</span></span>

<span data-ttu-id="7f17c-522">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7f17c-523">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-524">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7f17c-525">`AddHeaderAttribute` ' İ bir denetleyiciye veya eylem yöntemine UYGULAYıN ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="7f17c-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="7f17c-526">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7f17c-527">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7f17c-528">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="7f17c-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="7f17c-529">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7f17c-530">Bir eylem üzerinde bir öznitelik kullanma.</span><span class="sxs-lookup"><span data-stu-id="7f17c-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="7f17c-531">Bir denetleyicide bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="7f17c-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="7f17c-532">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler ve eylemler için genel olarak:</span><span class="sxs-lookup"><span data-stu-id="7f17c-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7f17c-533">Yukarıdaki kod, [Mvcoptions. Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) koleksiyonunu kullanarak genel olarak üç filtre ekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="7f17c-534">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="7f17c-534">Default order of execution</span></span>

<span data-ttu-id="7f17c-535">*Aynı türde*birden çok filtre olduğunda kapsam, filtre yürütmenin varsayılan sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7f17c-536">Genel filtreler saran sınıf filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-536">Global filters surround class filters.</span></span> <span data-ttu-id="7f17c-537">Sınıf filtreleri surround yöntemi filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-537">Class filters surround method filters.</span></span>

<span data-ttu-id="7f17c-538">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7f17c-539">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="7f17c-539">The filter sequence:</span></span>

* <span data-ttu-id="7f17c-540">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7f17c-541">Denetleyici filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="7f17c-542">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7f17c-543">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7f17c-544">Denetleyici filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="7f17c-545">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7f17c-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7f17c-546">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="7f17c-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7f17c-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="7f17c-547">Sequence</span></span> | <span data-ttu-id="7f17c-548">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7f17c-548">Filter scope</span></span> | <span data-ttu-id="7f17c-549">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7f17c-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7f17c-550">1</span><span class="sxs-lookup"><span data-stu-id="7f17c-550">1</span></span> | <span data-ttu-id="7f17c-551">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-552">2</span><span class="sxs-lookup"><span data-stu-id="7f17c-552">2</span></span> | <span data-ttu-id="7f17c-553">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7f17c-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-554">3</span><span class="sxs-lookup"><span data-stu-id="7f17c-554">3</span></span> | <span data-ttu-id="7f17c-555">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-556">4</span><span class="sxs-lookup"><span data-stu-id="7f17c-556">4</span></span> | <span data-ttu-id="7f17c-557">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7f17c-558">5</span><span class="sxs-lookup"><span data-stu-id="7f17c-558">5</span></span> | <span data-ttu-id="7f17c-559">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7f17c-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7f17c-560">6</span><span class="sxs-lookup"><span data-stu-id="7f17c-560">6</span></span> | <span data-ttu-id="7f17c-561">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="7f17c-562">Bu sıra şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-562">This sequence shows:</span></span>

* <span data-ttu-id="7f17c-563">Yöntem filtresi, denetleyici filtresi içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="7f17c-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="7f17c-564">Denetleyici filtresi, genel filtrenin içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="7f17c-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="7f17c-565">Denetleyici ve Razor sayfa düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="7f17c-566"><xref:Microsoft.AspNetCore.Mvc.Controller> Temel sınıftan devralan her denetleyici [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7f17c-567">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-567">These methods:</span></span>

* <span data-ttu-id="7f17c-568">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7f17c-569">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7f17c-570">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7f17c-571">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7f17c-572">Eylem yöntemiyle çalıştıktan sonra `next` filtredeki kod.</span><span class="sxs-lookup"><span data-stu-id="7f17c-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7f17c-573">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7f17c-574">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-574">The `TestController`:</span></span>

* <span data-ttu-id="7f17c-575">`FilterTest2` Eyleme `SampleActionFilterAttribute` (`[SampleActionFilter]`) uygular.</span><span class="sxs-lookup"><span data-stu-id="7f17c-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7f17c-576">Geçersiz `OnActionExecuting` kılmalar `OnActionExecuted`ve.</span><span class="sxs-lookup"><span data-stu-id="7f17c-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7f17c-577">' A `https://localhost:5001/Test/FilterTest2` gitme aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7f17c-578">Razor Pages için bkz. [filtre yöntemlerini geçersiz kılarak Razor sayfası filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7f17c-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7f17c-579">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="7f17c-579">Overriding the default order</span></span>

<span data-ttu-id="7f17c-580">Varsayılan yürütme sırası, uygulama <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7f17c-581">`IOrderedFilter`yürütme sırasını <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7f17c-582">Düşük `Order` değere sahip bir filtre:</span><span class="sxs-lookup"><span data-stu-id="7f17c-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7f17c-583">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order`.</span><span class="sxs-lookup"><span data-stu-id="7f17c-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7f17c-584">Daha yüksek *after* `Order` bir değere sahip bir filtrenin sonrasında koddan sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7f17c-585">`Order` Özelliği bir Oluşturucu parametresiyle ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="7f17c-586">Yukarıdaki örnekte gösterilen 3 eylem filtresini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="7f17c-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="7f17c-587">Denetleyicinin ve `Order` genel filtrelerin özelliği sırasıyla 1 ve 2 olarak ayarlandıysa, yürütme sırası tersine çevrilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="7f17c-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="7f17c-588">Sequence</span></span> | <span data-ttu-id="7f17c-589">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7f17c-589">Filter scope</span></span> | <span data-ttu-id="7f17c-590">`Order`özelliði</span><span class="sxs-lookup"><span data-stu-id="7f17c-590">`Order` property</span></span> | <span data-ttu-id="7f17c-591">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7f17c-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="7f17c-592">1</span><span class="sxs-lookup"><span data-stu-id="7f17c-592">1</span></span> | <span data-ttu-id="7f17c-593">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-593">Method</span></span> | <span data-ttu-id="7f17c-594">0</span><span class="sxs-lookup"><span data-stu-id="7f17c-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7f17c-595">2</span><span class="sxs-lookup"><span data-stu-id="7f17c-595">2</span></span> | <span data-ttu-id="7f17c-596">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7f17c-596">Controller</span></span> | <span data-ttu-id="7f17c-597">1</span><span class="sxs-lookup"><span data-stu-id="7f17c-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7f17c-598">3</span><span class="sxs-lookup"><span data-stu-id="7f17c-598">3</span></span> | <span data-ttu-id="7f17c-599">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-599">Global</span></span> | <span data-ttu-id="7f17c-600">2</span><span class="sxs-lookup"><span data-stu-id="7f17c-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7f17c-601">4</span><span class="sxs-lookup"><span data-stu-id="7f17c-601">4</span></span> | <span data-ttu-id="7f17c-602">Genel</span><span class="sxs-lookup"><span data-stu-id="7f17c-602">Global</span></span> | <span data-ttu-id="7f17c-603">2</span><span class="sxs-lookup"><span data-stu-id="7f17c-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7f17c-604">5</span><span class="sxs-lookup"><span data-stu-id="7f17c-604">5</span></span> | <span data-ttu-id="7f17c-605">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7f17c-605">Controller</span></span> | <span data-ttu-id="7f17c-606">1</span><span class="sxs-lookup"><span data-stu-id="7f17c-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7f17c-607">6</span><span class="sxs-lookup"><span data-stu-id="7f17c-607">6</span></span> | <span data-ttu-id="7f17c-608">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7f17c-608">Method</span></span> | <span data-ttu-id="7f17c-609">0</span><span class="sxs-lookup"><span data-stu-id="7f17c-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="7f17c-610">`Order` Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7f17c-611">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7f17c-612">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7f17c-613">Yerleşik filtreler için kapsam, sıfır olmayan bir değere ayarlanmadığı `Order` takdirde sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7f17c-614">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="7f17c-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7f17c-615">Filtre işlem hattı, filtre yöntemine sunulan <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7f17c-616">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="7f17c-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-617">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7f17c-618">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7f17c-619">İlk olarak bir kaynak filtresi olduğundan ve `AddHeader` bir eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7f17c-620">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7f17c-621">Bu nedenle `AddHeader` , filtre `SomeResource` eylem için hiçbir şekilde çalışmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7f17c-622">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak `ShortCircuitingResourceFilter` çalıştırıldığında aynı olur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7f17c-623">İlk `ShortCircuitingResourceFilter` olarak, filtre türü veya açıkça `Order` özelliğin kullanımı kullanılarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="7f17c-624">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7f17c-624">Dependency injection</span></span>

<span data-ttu-id="7f17c-625">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7f17c-626">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7f17c-627">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7f17c-628">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-628">A type-activated filter means:</span></span>

* <span data-ttu-id="7f17c-629">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-629">An instance is created for each request.</span></span>
* <span data-ttu-id="7f17c-630">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7f17c-631">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7f17c-632">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="7f17c-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7f17c-633">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7f17c-634">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7f17c-635">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7f17c-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7f17c-637">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7f17c-638">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-638">Loggers are available from DI.</span></span> <span data-ttu-id="7f17c-639">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7f17c-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7f17c-640">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7f17c-641">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="7f17c-641">Logging added to filters:</span></span>

* <span data-ttu-id="7f17c-642">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7f17c-643">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="7f17c-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7f17c-644">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="7f17c-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7f17c-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7f17c-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="7f17c-646">Hizmet filtresi uygulama türleri ' de `ConfigureServices`kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7f17c-647">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7f17c-648">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7f17c-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7f17c-649">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7f17c-650">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir `AddHeaderResultServiceFilter` filtrenin bir örneğini dı:</span><span class="sxs-lookup"><span data-stu-id="7f17c-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7f17c-651">Kullanırken `ServiceFilterAttribute`, [servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7f17c-652">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7f17c-653">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="7f17c-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7f17c-654">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7f17c-655">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="7f17c-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7f17c-656">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7f17c-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7f17c-658">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7f17c-659">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7f17c-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7f17c-660">TypeFilterAttribute</span></span>

<span data-ttu-id="7f17c-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7f17c-662">Kullanarak <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>türü başlatır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7f17c-663">Türler `TypeFilterAttribute` doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="7f17c-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7f17c-664">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7f17c-665">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7f17c-666">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7f17c-667">Kullanırken `TypeFilterAttribute`, [typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7f17c-668">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7f17c-669">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7f17c-670">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7f17c-671">Aşağıdaki örnek kullanarak `TypeFilterAttribute`bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7f17c-672">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-672">Authorization filters</span></span>

<span data-ttu-id="7f17c-673">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-673">Authorization filters:</span></span>

* <span data-ttu-id="7f17c-674">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7f17c-675">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-675">Control access to action methods.</span></span>
* <span data-ttu-id="7f17c-676">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="7f17c-677">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7f17c-678">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7f17c-679">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="7f17c-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="7f17c-680">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-680">Calls the authorization system.</span></span>
* <span data-ttu-id="7f17c-681">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-681">Does not authorize requests.</span></span>

<span data-ttu-id="7f17c-682">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="7f17c-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7f17c-683">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-683">The exception will not be handled.</span></span>
* <span data-ttu-id="7f17c-684">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7f17c-685">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="7f17c-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7f17c-686">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7f17c-687">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-687">Resource filters</span></span>

<span data-ttu-id="7f17c-688">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-688">Resource filters:</span></span>

* <span data-ttu-id="7f17c-689"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> da arabirimini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7f17c-690">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7f17c-691">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7f17c-692">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7f17c-693">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7f17c-694">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-694">Resource filter examples:</span></span>

* <span data-ttu-id="7f17c-695">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="7f17c-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7f17c-696">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7f17c-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7f17c-697">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="7f17c-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7f17c-698">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7f17c-699">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7f17c-700">Eylem filtreleri Razor sayfalara **uygulanmaz.**</span><span class="sxs-lookup"><span data-stu-id="7f17c-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7f17c-701">Sayfalar ve <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> destekler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7f17c-702">Daha fazla bilgi için bkz. [Sayfalar Için Razor filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7f17c-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7f17c-703">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-703">Action filters:</span></span>

* <span data-ttu-id="7f17c-704"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> Ya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> da arabirimini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7f17c-705">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7f17c-706">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7f17c-707">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7f17c-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-bir eylem yöntemine yönelik girişlerin okunmalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="7f17c-709"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7f17c-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>-Eylem `Result` yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="7f17c-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7f17c-711">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="7f17c-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7f17c-712">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7f17c-713">Ayarından `Result`farklı olarak, başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7f17c-714">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Ve `Controller` `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7f17c-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="7f17c-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7f17c-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="7f17c-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7f17c-717">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-717">Setting this property to null:</span></span>

  * <span data-ttu-id="7f17c-718">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7f17c-719">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7f17c-720">Bir `IAsyncActionFilter`için bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="7f17c-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7f17c-721">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7f17c-722">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7f17c-723">Kısa devre dışı, bir sonuç <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="7f17c-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7f17c-724">Çerçeve, alt sınıflı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> olabilecek bir Özet sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7f17c-725">`OnActionExecuting` Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7f17c-726">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-726">Validate model state.</span></span>
* <span data-ttu-id="7f17c-727">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7f17c-728">`OnActionExecuted` Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7f17c-729">Ve, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7f17c-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7f17c-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7f17c-732">Null `Exception` olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7f17c-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7f17c-733">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7f17c-734">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7f17c-735">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-735">Exception filters</span></span>

<span data-ttu-id="7f17c-736">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-736">Exception filters:</span></span>

* <span data-ttu-id="7f17c-737">Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="7f17c-738">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7f17c-739">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7f17c-740">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-740">Exception filters:</span></span>

* <span data-ttu-id="7f17c-741">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="7f17c-741">Don't have before and after events.</span></span>
* <span data-ttu-id="7f17c-742">Veya <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>uygulayın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7f17c-743">Razor Sayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7f17c-744">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="7f17c-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7f17c-745">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini bir yanıt `true` olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7f17c-746">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7f17c-746">This stops propagation of the exception.</span></span> <span data-ttu-id="7f17c-747">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="7f17c-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7f17c-748">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-748">Only an action filter can do that.</span></span>

<span data-ttu-id="7f17c-749">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-749">Exception filters:</span></span>

* <span data-ttu-id="7f17c-750">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7f17c-751">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7f17c-752">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7f17c-753">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7f17c-754">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7f17c-755">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7f17c-756">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="7f17c-756">Result filters</span></span>

<span data-ttu-id="7f17c-757">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7f17c-757">Result filters:</span></span>

* <span data-ttu-id="7f17c-758">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="7f17c-758">Implement an interface:</span></span>
  * <span data-ttu-id="7f17c-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7f17c-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7f17c-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7f17c-761">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7f17c-762">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="7f17c-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7f17c-763">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7f17c-764">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7f17c-765">Bir görünüm döndüren bir eylem, <xref:Microsoft.AspNetCore.Mvc.ViewResult> çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7f17c-766">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7f17c-767">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7f17c-768">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7f17c-769">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="7f17c-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="7f17c-770">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7f17c-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7f17c-771">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7f17c-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Yöntemi, olarak <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true`ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7f17c-773">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7f17c-774">Bir özel durum oluşturmak `IResultFilter.OnResultExecuting` için şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="7f17c-775">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7f17c-776">Başarılı bir sonuç yerine hata olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7f17c-777"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> Yöntem çalıştığında, yanıt zaten istemciye gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="7f17c-778">Yanıt istemciye zaten gönderildiyse, daha fazla değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="7f17c-779">`ResultExecutedContext.Canceled`eylem sonucu yürütmesi `true` başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7f17c-780">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7f17c-781">Null `Exception` olarak ayarlanması bir özel durumu işler ve işlem hattının ilerleyen kısımlarında ASP.NET Core özel durumun yeniden oluşturulmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="7f17c-782">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7f17c-783">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7f17c-784">Bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>için bir çağrısı `await next` , sonraki sonuç filtrelerini <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="7f17c-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7f17c-785">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="7f17c-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7f17c-786">Çerçeve, alt sınıflı `ResultFilterAttribute` olabilecek bir Özet sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7f17c-787">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7f17c-788">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="7f17c-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7f17c-789">Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, tüm eylem <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7f17c-790">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-790">This includes action results produced by:</span></span>

* <span data-ttu-id="7f17c-791">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7f17c-792">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7f17c-792">Exception filters.</span></span>

<span data-ttu-id="7f17c-793">Örneğin, aşağıdaki filtre her zaman çalışır ve içerik anlaşması başarısız olduğunda<xref:Microsoft.AspNetCore.Mvc.ObjectResult> *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="7f17c-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7f17c-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7f17c-794">IFilterFactory</span></span>

<span data-ttu-id="7f17c-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7f17c-796">Bu nedenle, `IFilterFactory` bir örnek, filtre ardışık düzeninde `IFilterMetadata` herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7f17c-797">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir `IFilterFactory`öğesine dönüştürmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="7f17c-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7f17c-798">Atama başarılı olursa, çağrılan <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> `IFilterMetadata` örneği oluşturmak için yöntemi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7f17c-799">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7f17c-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7f17c-800">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7f17c-801">Önceki kod, [indirme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)çalıştırılarak test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="7f17c-802">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7f17c-803">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="7f17c-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7f17c-804">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="7f17c-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7f17c-805">**Yazar:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="7f17c-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="7f17c-806">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7f17c-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7f17c-807">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7f17c-807">**internal:** `My header`</span></span>

<span data-ttu-id="7f17c-808">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7f17c-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7f17c-809">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="7f17c-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7f17c-810">Uygulayan `IFilterFactory` filtreler şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7f17c-811">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7f17c-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="7f17c-812">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7f17c-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7f17c-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7f17c-814">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> örnek oluşturma <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> yöntemini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7f17c-815">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="7f17c-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7f17c-816">Aşağıdaki kod, `[SampleActionFilter]`uygulamak için üç yaklaşımda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="7f17c-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7f17c-817">Yukarıdaki kodda, yöntemi ile `[SampleActionFilter]` dekorasyon, uygulamak için tercih edilen yaklaşımdır. `SampleActionFilter`</span><span class="sxs-lookup"><span data-stu-id="7f17c-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7f17c-818">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="7f17c-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7f17c-819">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7f17c-820">Ancak filtreler, ASP.NET Core çalışma zamanının bir parçası olan ara yazılımlar dışında farklılık gösterir, bu da ASP.NET Core bağlamına ve yapılarına erişime sahip oldukları anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="7f17c-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="7f17c-821">Ara yazılımı bir filtre olarak kullanmak için, filtre ardışık düzenine eklenecek `Configure` olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7f17c-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7f17c-822">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="7f17c-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7f17c-823">Ara yazılımını <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7f17c-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7f17c-824">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7f17c-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7f17c-825">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="7f17c-825">Next actions</span></span>

* <span data-ttu-id="7f17c-826">[Sayfalar Için Razor filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="7f17c-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7f17c-827">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="7f17c-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
