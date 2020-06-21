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
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122171"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="137ac-103">ASP.NET Core filtreler</span><span class="sxs-lookup"><span data-stu-id="137ac-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="137ac-104">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="137ac-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="137ac-105">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="137ac-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="137ac-106">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="137ac-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="137ac-107">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="137ac-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="137ac-108">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="137ac-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="137ac-109">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="137ac-110">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="137ac-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="137ac-111">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="137ac-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="137ac-112">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="137ac-113">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="137ac-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="137ac-114">Filtreler doğrudan [ Razor bileşenlerle](xref:blazor/components/index)çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="137ac-115">Filtre, şu durumlarda bir bileşeni yalnızca dolaylı olarak etkileyebilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="137ac-116">Bileşen bir sayfa veya görünüme katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="137ac-117">Sayfa veya denetleyici/görünüm filtreyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="137ac-118">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) .</span><span class="sxs-lookup"><span data-stu-id="137ac-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="137ac-119">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="137ac-119">How filters work</span></span>

<span data-ttu-id="137ac-120">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="137ac-121">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve eylem çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="137ac-124">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="137ac-124">Filter types</span></span>

<span data-ttu-id="137ac-125">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="137ac-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="137ac-126">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="137ac-127">Yetkilendirme filtreleri, istek yetkilendirilmezse işlem hattı kısa devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="137ac-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="137ac-128">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="137ac-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="137ac-129">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="137ac-129">Run after authorization.</span></span>  
  * <span data-ttu-id="137ac-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanında kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="137ac-131">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="137ac-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="137ac-133">[Eylem filtreleri](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="137ac-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="137ac-134">Kodu bir eylem yöntemi çağrıldıktan hemen önce ve sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="137ac-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="137ac-135">Bir eyleme geçirilen bağımsız değişkenleri değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="137ac-136">Eylemden döndürülen sonucu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="137ac-137">Sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="137ac-138">[Özel durum filtreleri](#exception-filters) , yanıt gövdesinin üzerine yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygular.</span><span class="sxs-lookup"><span data-stu-id="137ac-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="137ac-139">[Sonuç filtreleri](#result-filters) , eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="137ac-140">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="137ac-141">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="137ac-142">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="137ac-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="137ac-145">Uygulama</span><span class="sxs-lookup"><span data-stu-id="137ac-145">Implementation</span></span>

<span data-ttu-id="137ac-146">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="137ac-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="137ac-147">Zaman uyumlu filtreler, ardışık düzen aşamasından önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="137ac-148">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="137ac-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="137ac-150">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="137ac-151">Örneğin <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="137ac-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="137ac-152">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="137ac-153">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="137ac-153">Multiple filter stages</span></span>

<span data-ttu-id="137ac-154">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="137ac-155">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="137ac-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="137ac-156">Zaman uyumlu: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ve<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="137ac-157">Zaman uyumsuz: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> ve<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="137ac-158">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="137ac-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="137ac-159">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="137ac-160">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="137ac-161">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="137ac-162">Gibi soyut sınıflar kullanırken <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , her bir filtre türü için yalnızca zaman uyumlu yöntemleri veya zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="137ac-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="137ac-163">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="137ac-163">Built-in filter attributes</span></span>

<span data-ttu-id="137ac-164">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="137ac-165">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="137ac-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-166">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="137ac-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="137ac-167">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="137ac-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="137ac-168">Üst bilgileri incelemek için [tarayıcı geliştirici araçları](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="137ac-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="137ac-169">**Yanıt üst bilgileri**altında `author: Rick Anderson` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="137ac-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="137ac-170">Aşağıdaki kod şunları uygular `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="137ac-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="137ac-171">Yapılandırma sistemindeki başlığı ve adı okur.</span><span class="sxs-lookup"><span data-stu-id="137ac-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="137ac-172">Önceki örnekten farklı olarak, aşağıdaki kod koda filtre parametrelerinin eklenmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="137ac-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="137ac-173">Başlık ve adı yanıt üstbilgisine ekler.</span><span class="sxs-lookup"><span data-stu-id="137ac-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-174">Yapılandırma seçenekleri, [Seçenekler deseninin](xref:fundamentals/configuration/options)kullanıldığı [yapılandırma sisteminden](xref:fundamentals/configuration/index) sağlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="137ac-175">Örneğin, dosyadaki *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="137ac-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="137ac-176">İçinde `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="137ac-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="137ac-177">`PositionOptions`Sınıfı, yapılandırma alanı ile hizmet kapsayıcısına eklenir `"Position"` .</span><span class="sxs-lookup"><span data-stu-id="137ac-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="137ac-178">, `MyActionFilterAttribute` Hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="137ac-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="137ac-179">Aşağıdaki kod, sınıfını göstermektedir `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="137ac-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="137ac-180">Aşağıdaki kod `MyActionFilterAttribute` yöntemi için geçerlidir `Index2` :</span><span class="sxs-lookup"><span data-stu-id="137ac-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="137ac-181">**Yanıt üst bilgileri**altında `author: Rick Anderson` , ve `Editor: Joe Smith` `Sample/Index2` uç nokta çağrıldığında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="137ac-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="137ac-182">Aşağıdaki kod, `MyActionFilterAttribute` ve `AddHeaderAttribute` Razor sayfasını sayfasına uygular:</span><span class="sxs-lookup"><span data-stu-id="137ac-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="137ac-183">Filtreler Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="137ac-184">Bunlar Razor sayfa modeline ya da genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="137ac-185">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="137ac-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="137ac-186">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="137ac-187">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="137ac-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="137ac-188">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="137ac-189">Bir denetleyici eyleminde bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="137ac-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="137ac-190">Filtre öznitelikleri, Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="137ac-191">Bir denetleyici veya sayfada bir özniteliği kullanma Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="137ac-192">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler, Eylemler ve sayfalar için genel olarak Razor :</span><span class="sxs-lookup"><span data-stu-id="137ac-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="137ac-193">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="137ac-193">Default order of execution</span></span>

<span data-ttu-id="137ac-194">İşlem hattının belirli bir aşamasına ilişkin birden çok filtre olduğunda, kapsam varsayılan filtre yürütme sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="137ac-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="137ac-195">Genel filtreler kapsayan sınıf filtreleri, bu da kapsayan Yöntem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="137ac-196">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="137ac-197">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="137ac-197">The filter sequence:</span></span>

* <span data-ttu-id="137ac-198">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="137ac-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="137ac-199">Denetleyicinin ve sayfa filtrelerinin *öncesindeki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="137ac-200">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="137ac-201">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="137ac-202">Denetleyicinin ve sayfa filtrelerinin *sonraki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="137ac-203">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="137ac-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="137ac-204">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="137ac-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="137ac-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="137ac-205">Sequence</span></span> | <span data-ttu-id="137ac-206">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="137ac-206">Filter scope</span></span> | <span data-ttu-id="137ac-207">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="137ac-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="137ac-208">1</span><span class="sxs-lookup"><span data-stu-id="137ac-208">1</span></span> | <span data-ttu-id="137ac-209">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-210">2</span><span class="sxs-lookup"><span data-stu-id="137ac-210">2</span></span> | <span data-ttu-id="137ac-211">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="137ac-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="137ac-212">3</span><span class="sxs-lookup"><span data-stu-id="137ac-212">3</span></span> | <span data-ttu-id="137ac-213">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-214">4</span><span class="sxs-lookup"><span data-stu-id="137ac-214">4</span></span> | <span data-ttu-id="137ac-215">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="137ac-216">5</span><span class="sxs-lookup"><span data-stu-id="137ac-216">5</span></span> | <span data-ttu-id="137ac-217">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="137ac-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="137ac-218">6</span><span class="sxs-lookup"><span data-stu-id="137ac-218">6</span></span> | <span data-ttu-id="137ac-219">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="137ac-220">Denetleyici düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-220">Controller level filters</span></span>

<span data-ttu-id="137ac-221">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="137ac-222">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="137ac-222">These methods:</span></span>

* <span data-ttu-id="137ac-223">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="137ac-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="137ac-224">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="137ac-225">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="137ac-226">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="137ac-227">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="137ac-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="137ac-228">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="137ac-229">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="137ac-229">The `TestController`:</span></span>

* <span data-ttu-id="137ac-230">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="137ac-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="137ac-231">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="137ac-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="137ac-232">' A gitme `https://localhost:5001/Test2/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="137ac-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="137ac-233">Denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini olarak ayarlar `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="137ac-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="137ac-234">Denetleyici düzeyi filtreleri metotlara uygulandıktan sonra çalıştırılacak şekilde ayarlanamaz. **not**</span><span class="sxs-lookup"><span data-stu-id="137ac-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="137ac-235">Sıra, sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="137ac-235">Order is explained in the next section.</span></span>

<span data-ttu-id="137ac-236">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="137ac-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="137ac-237">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="137ac-237">Overriding the default order</span></span>

<span data-ttu-id="137ac-238">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="137ac-239">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="137ac-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="137ac-240">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="137ac-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="137ac-241">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="137ac-242">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="137ac-243">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="137ac-244">Aşağıdaki denetleyicide iki eylem filtresini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="137ac-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="137ac-245">İçinde genel bir filtre eklenir `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="137ac-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="137ac-246">3 filtre aşağıdaki sırayla çalışır:</span><span class="sxs-lookup"><span data-stu-id="137ac-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="137ac-247">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="137ac-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="137ac-248">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="137ac-249">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="137ac-250">Daha önce belirtildiği gibi, denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini `int.MinValue` yerleşik filtreler için olarak ayarlar, `Order` sıfır olmayan bir değere ayarlanmadığı sürece kapsam sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="137ac-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="137ac-251">Önceki kodda, `MySampleActionFilter` `MyAction2FilterAttribute` Denetleyici kapsamı olan, daha önce çalışması için genel kapsama sahiptir.</span><span class="sxs-lookup"><span data-stu-id="137ac-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="137ac-252">`MyAction2FilterAttribute`Önce çalıştırmak için, sırayı şu şekilde ayarlayın `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="137ac-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="137ac-253">Önce genel filtrenin çalışmasını sağlamak için şu `MySampleActionFilter` şekilde ayarlayın `Order` `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="137ac-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="137ac-254">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="137ac-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="137ac-255">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="137ac-256">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="137ac-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-257">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="137ac-258">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="137ac-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="137ac-259">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="137ac-260">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="137ac-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="137ac-261">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="137ac-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="137ac-262">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="137ac-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="137ac-263">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="137ac-264">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="137ac-264">Dependency injection</span></span>

<span data-ttu-id="137ac-265">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="137ac-266">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="137ac-267">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="137ac-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="137ac-268">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="137ac-268">A type-activated filter means:</span></span>

* <span data-ttu-id="137ac-269">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="137ac-269">An instance is created for each request.</span></span>
* <span data-ttu-id="137ac-270">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="137ac-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="137ac-271">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="137ac-272">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="137ac-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="137ac-273">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="137ac-274">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="137ac-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="137ac-275">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="137ac-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="137ac-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="137ac-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="137ac-277">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="137ac-278">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-278">Loggers are available from DI.</span></span> <span data-ttu-id="137ac-279">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="137ac-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="137ac-280">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="137ac-281">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="137ac-281">Logging added to filters:</span></span>

* <span data-ttu-id="137ac-282">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="137ac-283">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="137ac-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="137ac-284">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="137ac-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="137ac-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="137ac-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="137ac-286">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="137ac-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="137ac-287">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="137ac-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="137ac-288">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="137ac-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="137ac-289">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="137ac-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="137ac-290">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="137ac-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="137ac-291">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="137ac-292">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="137ac-293">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="137ac-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="137ac-294">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="137ac-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="137ac-295">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="137ac-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="137ac-296">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="137ac-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="137ac-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="137ac-298">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="137ac-299">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="137ac-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="137ac-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="137ac-300">TypeFilterAttribute</span></span>

<span data-ttu-id="137ac-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="137ac-302">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="137ac-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="137ac-303">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="137ac-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="137ac-304">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="137ac-305">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="137ac-306">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="137ac-307">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="137ac-308">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="137ac-309">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="137ac-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="137ac-310">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="137ac-311">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="137ac-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="137ac-312">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-312">Authorization filters</span></span>

<span data-ttu-id="137ac-313">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-313">Authorization filters:</span></span>

* <span data-ttu-id="137ac-314">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="137ac-315">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-315">Control access to action methods.</span></span>
* <span data-ttu-id="137ac-316">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="137ac-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="137ac-317">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="137ac-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="137ac-318">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="137ac-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="137ac-319">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="137ac-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="137ac-320">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-320">Calls the authorization system.</span></span>
* <span data-ttu-id="137ac-321">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-321">Does not authorize requests.</span></span>

<span data-ttu-id="137ac-322">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="137ac-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="137ac-323">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="137ac-323">The exception will not be handled.</span></span>
* <span data-ttu-id="137ac-324">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="137ac-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="137ac-325">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="137ac-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="137ac-326">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="137ac-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="137ac-327">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-327">Resource filters</span></span>

<span data-ttu-id="137ac-328">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-328">Resource filters:</span></span>

* <span data-ttu-id="137ac-329"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="137ac-330">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="137ac-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="137ac-331">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="137ac-332">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="137ac-333">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="137ac-334">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-334">Resource filter examples:</span></span>

* <span data-ttu-id="137ac-335">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="137ac-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="137ac-336">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="137ac-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="137ac-337">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="137ac-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="137ac-338">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="137ac-339">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-339">Action filters</span></span>

<span data-ttu-id="137ac-340">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="137ac-341">Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="137ac-342">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="137ac-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="137ac-343">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-343">Action filters:</span></span>

* <span data-ttu-id="137ac-344"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="137ac-345">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="137ac-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="137ac-346">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="137ac-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="137ac-347">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="137ac-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-girişlerin bir eylem yöntemine okunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="137ac-349"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="137ac-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="137ac-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>- `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="137ac-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="137ac-351">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="137ac-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="137ac-352">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="137ac-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="137ac-353">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="137ac-354">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="137ac-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="137ac-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="137ac-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="137ac-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="137ac-357">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-357">Setting this property to null:</span></span>

  * <span data-ttu-id="137ac-358">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="137ac-359">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="137ac-360">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="137ac-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="137ac-361">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="137ac-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="137ac-362">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="137ac-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="137ac-363">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="137ac-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="137ac-364">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="137ac-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="137ac-365">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="137ac-366">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="137ac-366">Validate model state.</span></span>
* <span data-ttu-id="137ac-367">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="137ac-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="137ac-368">Öznitelik ile ek açıklama eklenmiş denetleyiciler `[ApiController]` model durumunu otomatik olarak doğrular ve bir 400 yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="137ac-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="137ac-369">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="137ac-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="137ac-370">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="137ac-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="137ac-371">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="137ac-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="137ac-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="137ac-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="137ac-374">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="137ac-375">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="137ac-376">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="137ac-377">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-377">Exception filters</span></span>

<span data-ttu-id="137ac-378">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-378">Exception filters:</span></span>

* <span data-ttu-id="137ac-379"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="137ac-380">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="137ac-381">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="137ac-382">Aşağıdaki kod özel durum filtresini sınar:</span><span class="sxs-lookup"><span data-stu-id="137ac-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="137ac-383">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-383">Exception filters:</span></span>

* <span data-ttu-id="137ac-384">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="137ac-384">Don't have before and after events.</span></span>
* <span data-ttu-id="137ac-385"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="137ac-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="137ac-386">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="137ac-387">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="137ac-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="137ac-388">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="137ac-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="137ac-389">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="137ac-389">This stops propagation of the exception.</span></span> <span data-ttu-id="137ac-390">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="137ac-391">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-391">Only an action filter can do that.</span></span>

<span data-ttu-id="137ac-392">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-392">Exception filters:</span></span>

* <span data-ttu-id="137ac-393">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="137ac-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="137ac-394">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="137ac-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="137ac-395">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="137ac-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="137ac-396">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="137ac-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="137ac-397">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="137ac-398">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="137ac-399">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-399">Result filters</span></span>

<span data-ttu-id="137ac-400">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-400">Result filters:</span></span>

* <span data-ttu-id="137ac-401">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="137ac-401">Implement an interface:</span></span>
  * <span data-ttu-id="137ac-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="137ac-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="137ac-404">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="137ac-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="137ac-405">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="137ac-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="137ac-406">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="137ac-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="137ac-407">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="137ac-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="137ac-408">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="137ac-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="137ac-409">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="137ac-410">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="137ac-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="137ac-411">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="137ac-412">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="137ac-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="137ac-413">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="137ac-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="137ac-414">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="137ac-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="137ac-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="137ac-416">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="137ac-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="137ac-417">İçinde bir özel durum üretiliyor `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="137ac-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="137ac-418">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önler.</span><span class="sxs-lookup"><span data-stu-id="137ac-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="137ac-419">Başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="137ac-420"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt muhtemelen istemciye zaten gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="137ac-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="137ac-421">Yanıt istemciye zaten gönderildiyse, bu değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="137ac-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="137ac-422">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="137ac-423">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="137ac-424">`Exception`Null olarak ayarlanması bir özel durumu işler ve sonra işlem hattının daha sonra yeniden oluşturulmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="137ac-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="137ac-425">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="137ac-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="137ac-426">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="137ac-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="137ac-427">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="137ac-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="137ac-428">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="137ac-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="137ac-429">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="137ac-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="137ac-430">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="137ac-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="137ac-431">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="137ac-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="137ac-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="137ac-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="137ac-433">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="137ac-433">This includes action results produced by:</span></span>

* <span data-ttu-id="137ac-434">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="137ac-435">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-435">Exception filters.</span></span>

<span data-ttu-id="137ac-436">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="137ac-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="137ac-437">IFilterFactory</span></span>

<span data-ttu-id="137ac-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="137ac-439">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="137ac-440">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="137ac-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="137ac-441">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="137ac-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="137ac-442">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="137ac-443">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="137ac-444">Filtre aşağıdaki kodda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="137ac-445">Önceki kodu [indirme örneğini](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)çalıştırarak test edin:</span><span class="sxs-lookup"><span data-stu-id="137ac-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="137ac-446">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="137ac-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="137ac-447">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="137ac-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="137ac-448">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="137ac-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="137ac-449">**Yazar:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="137ac-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="137ac-450">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="137ac-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="137ac-451">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="137ac-451">**internal:** `My header`</span></span>

<span data-ttu-id="137ac-452">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="137ac-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="137ac-453">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="137ac-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="137ac-454">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="137ac-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="137ac-455">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="137ac-456">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="137ac-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="137ac-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="137ac-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="137ac-458">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="137ac-459">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="137ac-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="137ac-460">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="137ac-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="137ac-461">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="137ac-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="137ac-462">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="137ac-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="137ac-463">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="137ac-464">Ancak filtreler, çalışma zamanının bir parçası oldukları ve bu sayede bağlam ve yapılara erişimi olan ara yazılımlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="137ac-465">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="137ac-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="137ac-466">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="137ac-467"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="137ac-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="137ac-468">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="137ac-469">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="137ac-469">Next actions</span></span>

* <span data-ttu-id="137ac-470">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="137ac-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="137ac-471">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="137ac-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="137ac-472">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="137ac-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="137ac-473">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="137ac-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="137ac-474">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="137ac-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="137ac-475">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="137ac-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="137ac-476">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="137ac-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="137ac-477">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="137ac-478">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="137ac-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="137ac-479">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="137ac-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="137ac-480">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="137ac-481">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="137ac-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="137ac-482">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) .</span><span class="sxs-lookup"><span data-stu-id="137ac-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="137ac-483">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="137ac-483">How filters work</span></span>

<span data-ttu-id="137ac-484">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="137ac-485">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve ASP.NET Core eylemi çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="137ac-488">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="137ac-488">Filter types</span></span>

<span data-ttu-id="137ac-489">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="137ac-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="137ac-490">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="137ac-491">Yetkilendirme filtreleri, istek yetkisiz ise işlem hattı kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="137ac-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="137ac-492">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="137ac-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="137ac-493">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="137ac-493">Run after authorization.</span></span>  
  * <span data-ttu-id="137ac-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanından önce kod çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="137ac-495">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="137ac-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="137ac-497">[Eylem filtreleri](#action-filters) , tek bir eylem yöntemi çağrıldıktan hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="137ac-498">Bir eyleme geçirilen bağımsız değişkenleri ve eylemden döndürülen sonucu işlemek için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="137ac-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="137ac-499">Eylem filtreleri sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="137ac-500">[Özel durum filtreleri](#exception-filters) , yanıt gövdesine hiçbir şey yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="137ac-501">[Sonuç filtreleri](#result-filters) , tek tek eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="137ac-502">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="137ac-503">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="137ac-504">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="137ac-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="137ac-507">Uygulama</span><span class="sxs-lookup"><span data-stu-id="137ac-507">Implementation</span></span>

<span data-ttu-id="137ac-508">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="137ac-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="137ac-509">Zaman uyumlu filtreler `On-Stage-Executing` `On-Stage-Executed` , ardışık düzen aşamasını () ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="137ac-510">Örneğin, `OnActionExecuting` eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="137ac-511">`OnActionExecuted`, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="137ac-512">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="137ac-513">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="137ac-514">Yöntemlerin her biri, `On-Stage-ExecutionAsync` `FilterType-ExecutionDelegate` filtrenin ardışık düzen aşamasını yürüten bir alır.</span><span class="sxs-lookup"><span data-stu-id="137ac-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="137ac-515">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="137ac-515">Multiple filter stages</span></span>

<span data-ttu-id="137ac-516">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="137ac-517">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı `IActionFilter` , `IResultFilter` ve zaman uyumsuz eşdeğerlerini uygular.</span><span class="sxs-lookup"><span data-stu-id="137ac-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="137ac-518">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="137ac-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="137ac-519">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="137ac-520">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="137ac-521">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="137ac-522">Soyut sınıfların kullanıldığı durumlarda <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="137ac-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="137ac-523">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="137ac-523">Built-in filter attributes</span></span>

<span data-ttu-id="137ac-524">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="137ac-525">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="137ac-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-526">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="137ac-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="137ac-527">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="137ac-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="137ac-528">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="137ac-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="137ac-529">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="137ac-530">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="137ac-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="137ac-531">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="137ac-532">Bir eylem üzerinde bir öznitelik kullanma.</span><span class="sxs-lookup"><span data-stu-id="137ac-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="137ac-533">Bir denetleyicide bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="137ac-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="137ac-534">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler ve eylemler için genel olarak:</span><span class="sxs-lookup"><span data-stu-id="137ac-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="137ac-535">Yukarıdaki kod, [Mvcoptions. Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) koleksiyonunu kullanarak genel olarak üç filtre ekler.</span><span class="sxs-lookup"><span data-stu-id="137ac-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="137ac-536">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="137ac-536">Default order of execution</span></span>

<span data-ttu-id="137ac-537">*Aynı türde*birden çok filtre olduğunda kapsam, filtre yürütmenin varsayılan sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="137ac-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="137ac-538">Genel filtreler saran sınıf filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-538">Global filters surround class filters.</span></span> <span data-ttu-id="137ac-539">Sınıf filtreleri surround yöntemi filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-539">Class filters surround method filters.</span></span>

<span data-ttu-id="137ac-540">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="137ac-541">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="137ac-541">The filter sequence:</span></span>

* <span data-ttu-id="137ac-542">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="137ac-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="137ac-543">Denetleyici filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="137ac-544">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="137ac-545">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="137ac-546">Denetleyici filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="137ac-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="137ac-547">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="137ac-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="137ac-548">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="137ac-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="137ac-549">Sequence</span><span class="sxs-lookup"><span data-stu-id="137ac-549">Sequence</span></span> | <span data-ttu-id="137ac-550">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="137ac-550">Filter scope</span></span> | <span data-ttu-id="137ac-551">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="137ac-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="137ac-552">1</span><span class="sxs-lookup"><span data-stu-id="137ac-552">1</span></span> | <span data-ttu-id="137ac-553">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-554">2</span><span class="sxs-lookup"><span data-stu-id="137ac-554">2</span></span> | <span data-ttu-id="137ac-555">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="137ac-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-556">3</span><span class="sxs-lookup"><span data-stu-id="137ac-556">3</span></span> | <span data-ttu-id="137ac-557">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-558">4</span><span class="sxs-lookup"><span data-stu-id="137ac-558">4</span></span> | <span data-ttu-id="137ac-559">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="137ac-560">5</span><span class="sxs-lookup"><span data-stu-id="137ac-560">5</span></span> | <span data-ttu-id="137ac-561">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="137ac-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="137ac-562">6</span><span class="sxs-lookup"><span data-stu-id="137ac-562">6</span></span> | <span data-ttu-id="137ac-563">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="137ac-564">Bu sıra şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="137ac-564">This sequence shows:</span></span>

* <span data-ttu-id="137ac-565">Yöntem filtresi, denetleyici filtresi içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="137ac-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="137ac-566">Denetleyici filtresi, genel filtrenin içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="137ac-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="137ac-567">Denetleyici ve Razor sayfa düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="137ac-568">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="137ac-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="137ac-569">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="137ac-569">These methods:</span></span>

* <span data-ttu-id="137ac-570">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="137ac-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="137ac-571">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="137ac-572">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="137ac-573">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="137ac-574">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="137ac-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="137ac-575">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="137ac-576">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="137ac-576">The `TestController`:</span></span>

* <span data-ttu-id="137ac-577">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="137ac-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="137ac-578">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="137ac-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="137ac-579">' A gitme `https://localhost:5001/Test/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="137ac-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="137ac-580">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="137ac-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="137ac-581">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="137ac-581">Overriding the default order</span></span>

<span data-ttu-id="137ac-582">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="137ac-583">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="137ac-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="137ac-584">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="137ac-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="137ac-585">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="137ac-586">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="137ac-587">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="137ac-588">Yukarıdaki örnekte gösterilen 3 eylem filtresini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="137ac-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="137ac-589">`Order`Denetleyicinin ve genel filtrelerin özelliği sırasıyla 1 ve 2 olarak ayarlandıysa, yürütme sırası tersine çevrilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="137ac-590">Sequence</span><span class="sxs-lookup"><span data-stu-id="137ac-590">Sequence</span></span> | <span data-ttu-id="137ac-591">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="137ac-591">Filter scope</span></span> | <span data-ttu-id="137ac-592">`Order`özelliði</span><span class="sxs-lookup"><span data-stu-id="137ac-592">`Order` property</span></span> | <span data-ttu-id="137ac-593">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="137ac-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="137ac-594">1</span><span class="sxs-lookup"><span data-stu-id="137ac-594">1</span></span> | <span data-ttu-id="137ac-595">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-595">Method</span></span> | <span data-ttu-id="137ac-596">0</span><span class="sxs-lookup"><span data-stu-id="137ac-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="137ac-597">2</span><span class="sxs-lookup"><span data-stu-id="137ac-597">2</span></span> | <span data-ttu-id="137ac-598">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="137ac-598">Controller</span></span> | <span data-ttu-id="137ac-599">1</span><span class="sxs-lookup"><span data-stu-id="137ac-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="137ac-600">3</span><span class="sxs-lookup"><span data-stu-id="137ac-600">3</span></span> | <span data-ttu-id="137ac-601">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-601">Global</span></span> | <span data-ttu-id="137ac-602">2</span><span class="sxs-lookup"><span data-stu-id="137ac-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="137ac-603">4</span><span class="sxs-lookup"><span data-stu-id="137ac-603">4</span></span> | <span data-ttu-id="137ac-604">Genel</span><span class="sxs-lookup"><span data-stu-id="137ac-604">Global</span></span> | <span data-ttu-id="137ac-605">2</span><span class="sxs-lookup"><span data-stu-id="137ac-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="137ac-606">5</span><span class="sxs-lookup"><span data-stu-id="137ac-606">5</span></span> | <span data-ttu-id="137ac-607">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="137ac-607">Controller</span></span> | <span data-ttu-id="137ac-608">1</span><span class="sxs-lookup"><span data-stu-id="137ac-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="137ac-609">6</span><span class="sxs-lookup"><span data-stu-id="137ac-609">6</span></span> | <span data-ttu-id="137ac-610">Yöntem</span><span class="sxs-lookup"><span data-stu-id="137ac-610">Method</span></span> | <span data-ttu-id="137ac-611">0</span><span class="sxs-lookup"><span data-stu-id="137ac-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="137ac-612">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="137ac-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="137ac-613">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="137ac-614">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="137ac-615">Yerleşik filtreler için kapsam, `Order` sıfır olmayan bir değere ayarlanmadığı takdirde sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="137ac-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="137ac-616">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="137ac-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="137ac-617">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="137ac-618">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="137ac-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-619">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="137ac-620">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="137ac-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="137ac-621">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="137ac-622">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="137ac-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="137ac-623">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="137ac-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="137ac-624">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="137ac-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="137ac-625">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="137ac-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="137ac-626">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="137ac-626">Dependency injection</span></span>

<span data-ttu-id="137ac-627">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="137ac-628">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="137ac-629">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="137ac-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="137ac-630">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="137ac-630">A type-activated filter means:</span></span>

* <span data-ttu-id="137ac-631">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="137ac-631">An instance is created for each request.</span></span>
* <span data-ttu-id="137ac-632">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="137ac-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="137ac-633">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="137ac-634">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="137ac-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="137ac-635">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="137ac-636">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="137ac-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="137ac-637">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="137ac-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="137ac-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="137ac-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="137ac-639">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="137ac-640">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-640">Loggers are available from DI.</span></span> <span data-ttu-id="137ac-641">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="137ac-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="137ac-642">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="137ac-643">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="137ac-643">Logging added to filters:</span></span>

* <span data-ttu-id="137ac-644">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="137ac-645">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="137ac-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="137ac-646">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="137ac-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="137ac-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="137ac-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="137ac-648">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="137ac-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="137ac-649">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="137ac-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="137ac-650">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="137ac-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="137ac-651">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="137ac-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="137ac-652">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="137ac-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="137ac-653">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="137ac-654">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="137ac-655">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="137ac-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="137ac-656">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="137ac-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="137ac-657">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="137ac-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="137ac-658">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="137ac-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="137ac-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="137ac-660">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="137ac-661">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="137ac-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="137ac-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="137ac-662">TypeFilterAttribute</span></span>

<span data-ttu-id="137ac-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="137ac-664">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="137ac-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="137ac-665">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="137ac-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="137ac-666">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="137ac-667">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="137ac-668">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="137ac-669">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="137ac-670">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="137ac-671">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="137ac-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="137ac-672">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="137ac-673">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="137ac-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="137ac-674">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-674">Authorization filters</span></span>

<span data-ttu-id="137ac-675">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-675">Authorization filters:</span></span>

* <span data-ttu-id="137ac-676">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="137ac-677">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-677">Control access to action methods.</span></span>
* <span data-ttu-id="137ac-678">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="137ac-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="137ac-679">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="137ac-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="137ac-680">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="137ac-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="137ac-681">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="137ac-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="137ac-682">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="137ac-682">Calls the authorization system.</span></span>
* <span data-ttu-id="137ac-683">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-683">Does not authorize requests.</span></span>

<span data-ttu-id="137ac-684">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="137ac-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="137ac-685">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="137ac-685">The exception will not be handled.</span></span>
* <span data-ttu-id="137ac-686">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="137ac-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="137ac-687">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="137ac-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="137ac-688">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="137ac-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="137ac-689">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-689">Resource filters</span></span>

<span data-ttu-id="137ac-690">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-690">Resource filters:</span></span>

* <span data-ttu-id="137ac-691"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="137ac-692">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="137ac-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="137ac-693">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="137ac-694">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="137ac-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="137ac-695">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="137ac-696">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-696">Resource filter examples:</span></span>

* <span data-ttu-id="137ac-697">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="137ac-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="137ac-698">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="137ac-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="137ac-699">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="137ac-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="137ac-700">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="137ac-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="137ac-701">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="137ac-702">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="137ac-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="137ac-703">Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="137ac-704">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="137ac-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="137ac-705">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-705">Action filters:</span></span>

* <span data-ttu-id="137ac-706"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="137ac-707">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="137ac-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="137ac-708">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="137ac-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="137ac-709">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="137ac-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-bir eylem yöntemine yönelik girişlerin okunmalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="137ac-711"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="137ac-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="137ac-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>- `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="137ac-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="137ac-713">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="137ac-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="137ac-714">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="137ac-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="137ac-715">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="137ac-716">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="137ac-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="137ac-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="137ac-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="137ac-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="137ac-719">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-719">Setting this property to null:</span></span>

  * <span data-ttu-id="137ac-720">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="137ac-721">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="137ac-722">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="137ac-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="137ac-723">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="137ac-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="137ac-724">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="137ac-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="137ac-725">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="137ac-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="137ac-726">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="137ac-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="137ac-727">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="137ac-728">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="137ac-728">Validate model state.</span></span>
* <span data-ttu-id="137ac-729">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="137ac-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="137ac-730">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="137ac-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="137ac-731">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="137ac-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="137ac-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="137ac-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="137ac-734">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="137ac-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="137ac-735">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="137ac-736">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="137ac-737">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-737">Exception filters</span></span>

<span data-ttu-id="137ac-738">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-738">Exception filters:</span></span>

* <span data-ttu-id="137ac-739"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="137ac-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="137ac-740">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="137ac-741">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="137ac-742">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-742">Exception filters:</span></span>

* <span data-ttu-id="137ac-743">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="137ac-743">Don't have before and after events.</span></span>
* <span data-ttu-id="137ac-744"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="137ac-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="137ac-745">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="137ac-746">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="137ac-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="137ac-747">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="137ac-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="137ac-748">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="137ac-748">This stops propagation of the exception.</span></span> <span data-ttu-id="137ac-749">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="137ac-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="137ac-750">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-750">Only an action filter can do that.</span></span>

<span data-ttu-id="137ac-751">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-751">Exception filters:</span></span>

* <span data-ttu-id="137ac-752">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="137ac-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="137ac-753">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="137ac-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="137ac-754">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="137ac-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="137ac-755">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="137ac-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="137ac-756">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="137ac-757">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="137ac-758">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="137ac-758">Result filters</span></span>

<span data-ttu-id="137ac-759">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="137ac-759">Result filters:</span></span>

* <span data-ttu-id="137ac-760">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="137ac-760">Implement an interface:</span></span>
  * <span data-ttu-id="137ac-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="137ac-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="137ac-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="137ac-763">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="137ac-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="137ac-764">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="137ac-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="137ac-765">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="137ac-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="137ac-766">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="137ac-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="137ac-767">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="137ac-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="137ac-768">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="137ac-769">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="137ac-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="137ac-770">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="137ac-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="137ac-771">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="137ac-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="137ac-772">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="137ac-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="137ac-773">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="137ac-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="137ac-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="137ac-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="137ac-775">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="137ac-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="137ac-776">Bir özel durum oluşturmak için `IResultFilter.OnResultExecuting` şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="137ac-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="137ac-777">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="137ac-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="137ac-778">Başarılı bir sonuç yerine hata olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="137ac-779"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt zaten istemciye gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="137ac-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="137ac-780">Yanıt istemciye zaten gönderildiyse, daha fazla değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="137ac-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="137ac-781">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="137ac-782">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="137ac-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="137ac-783">`Exception`Null olarak ayarlanması bir özel durumu işler ve işlem hattının ilerleyen kısımlarında ASP.NET Core özel durumun yeniden oluşturulmasını önler.</span><span class="sxs-lookup"><span data-stu-id="137ac-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="137ac-784">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="137ac-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="137ac-785">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="137ac-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="137ac-786">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="137ac-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="137ac-787">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="137ac-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="137ac-788">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="137ac-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="137ac-789">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="137ac-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="137ac-790">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="137ac-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="137ac-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="137ac-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="137ac-792">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="137ac-792">This includes action results produced by:</span></span>

* <span data-ttu-id="137ac-793">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="137ac-794">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="137ac-794">Exception filters.</span></span>

<span data-ttu-id="137ac-795">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="137ac-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="137ac-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="137ac-796">IFilterFactory</span></span>

<span data-ttu-id="137ac-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="137ac-798">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="137ac-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="137ac-799">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="137ac-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="137ac-800">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="137ac-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="137ac-801">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="137ac-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="137ac-802">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="137ac-803">Önceki kod, [indirme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)çalıştırılarak test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="137ac-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="137ac-804">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="137ac-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="137ac-805">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="137ac-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="137ac-806">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="137ac-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="137ac-807">**Yazar:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="137ac-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="137ac-808">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="137ac-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="137ac-809">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="137ac-809">**internal:** `My header`</span></span>

<span data-ttu-id="137ac-810">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="137ac-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="137ac-811">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="137ac-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="137ac-812">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="137ac-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="137ac-813">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="137ac-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="137ac-814">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="137ac-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="137ac-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="137ac-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="137ac-816">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="137ac-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="137ac-817">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="137ac-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="137ac-818">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="137ac-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="137ac-819">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="137ac-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="137ac-820">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="137ac-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="137ac-821">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="137ac-822">Ancak filtreler, ASP.NET Core çalışma zamanının bir parçası olan ara yazılımlar dışında farklılık gösterir, bu da ASP.NET Core bağlamına ve yapılarına erişime sahip oldukları anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="137ac-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="137ac-823">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="137ac-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="137ac-824">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="137ac-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="137ac-825"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="137ac-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="137ac-826">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="137ac-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="137ac-827">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="137ac-827">Next actions</span></span>

* <span data-ttu-id="137ac-828">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="137ac-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="137ac-829">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="137ac-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
