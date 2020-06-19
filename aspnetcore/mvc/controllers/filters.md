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
ms.openlocfilehash: 407583533939ec1077af8e1a1511ed187ef9de69
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103012"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="7040d-103">ASP.NET Core filtreler</span><span class="sxs-lookup"><span data-stu-id="7040d-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7040d-104">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="7040d-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7040d-105">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7040d-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7040d-106">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="7040d-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7040d-107">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="7040d-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7040d-108">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="7040d-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7040d-109">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7040d-110">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="7040d-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7040d-111">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7040d-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="7040d-112">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7040d-113">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7040d-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="7040d-114">Filtreler doğrudan [ Razor bileşenlerle](xref:blazor/components/index)çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="7040d-115">Filtre, şu durumlarda bir bileşeni yalnızca dolaylı olarak etkileyebilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="7040d-116">Bileşen bir sayfa veya görünüme katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="7040d-117">Sayfa veya denetleyici/görünüm filtreyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="7040d-118">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) .</span><span class="sxs-lookup"><span data-stu-id="7040d-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7040d-119">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="7040d-119">How filters work</span></span>

<span data-ttu-id="7040d-120">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="7040d-121">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve eylem çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7040d-124">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="7040d-124">Filter types</span></span>

<span data-ttu-id="7040d-125">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="7040d-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7040d-126">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7040d-127">Yetkilendirme filtreleri, istek yetkilendirilmezse işlem hattı kısa devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="7040d-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="7040d-128">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7040d-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7040d-129">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7040d-129">Run after authorization.</span></span>  
  * <span data-ttu-id="7040d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanında kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7040d-131">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="7040d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7040d-133">[Eylem filtreleri](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="7040d-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="7040d-134">Kodu bir eylem yöntemi çağrıldıktan hemen önce ve sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7040d-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="7040d-135">Bir eyleme geçirilen bağımsız değişkenleri değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="7040d-136">Eylemden döndürülen sonucu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="7040d-137">Sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7040d-138">[Özel durum filtreleri](#exception-filters) , yanıt gövdesinin üzerine yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygular.</span><span class="sxs-lookup"><span data-stu-id="7040d-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="7040d-139">[Sonuç filtreleri](#result-filters) , eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="7040d-140">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7040d-141">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7040d-142">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="7040d-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7040d-145">Uygulama</span><span class="sxs-lookup"><span data-stu-id="7040d-145">Implementation</span></span>

<span data-ttu-id="7040d-146">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="7040d-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7040d-147">Zaman uyumlu filtreler, ardışık düzen aşamasından önce ve sonra kodu çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="7040d-148">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="7040d-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7040d-150">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="7040d-151">Örneğin <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="7040d-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7040d-152">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7040d-153">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="7040d-153">Multiple filter stages</span></span>

<span data-ttu-id="7040d-154">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7040d-155">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="7040d-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="7040d-156">Zaman uyumlu: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ve<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="7040d-157">Zaman uyumsuz: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> ve<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="7040d-158">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="7040d-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7040d-159">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7040d-160">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7040d-161">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7040d-162">Gibi soyut sınıflar kullanırken <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , her bir filtre türü için yalnızca zaman uyumlu yöntemleri veya zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="7040d-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7040d-163">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="7040d-163">Built-in filter attributes</span></span>

<span data-ttu-id="7040d-164">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7040d-165">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="7040d-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-166">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="7040d-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7040d-167">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="7040d-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="7040d-168">Üst bilgileri incelemek için [tarayıcı geliştirici araçları](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) gibi bir araç kullanın.</span><span class="sxs-lookup"><span data-stu-id="7040d-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="7040d-169">**Yanıt üst bilgileri**altında `author: Rick Anderson` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7040d-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="7040d-170">Aşağıdaki kod şunları uygular `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7040d-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="7040d-171">Yapılandırma sistemindeki başlığı ve adı okur.</span><span class="sxs-lookup"><span data-stu-id="7040d-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="7040d-172">Önceki örnekten farklı olarak, aşağıdaki kod koda filtre parametrelerinin eklenmesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="7040d-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="7040d-173">Başlık ve adı yanıt üstbilgisine ekler.</span><span class="sxs-lookup"><span data-stu-id="7040d-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-174">Yapılandırma seçenekleri, [Seçenekler deseninin](xref:fundamentals/configuration/options)kullanıldığı [yapılandırma sisteminden](xref:fundamentals/configuration/index) sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7040d-175">Örneğin, dosyadaki *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="7040d-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="7040d-176">İçinde `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7040d-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="7040d-177">`PositionOptions`Sınıfı, yapılandırma alanı ile hizmet kapsayıcısına eklenir `"Position"` .</span><span class="sxs-lookup"><span data-stu-id="7040d-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="7040d-178">, `MyActionFilterAttribute` Hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="7040d-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="7040d-179">Aşağıdaki kod, sınıfını göstermektedir `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="7040d-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="7040d-180">Aşağıdaki kod `MyActionFilterAttribute` yöntemi için geçerlidir `Index2` :</span><span class="sxs-lookup"><span data-stu-id="7040d-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="7040d-181">**Yanıt üst bilgileri**altında `author: Rick Anderson` , ve `Editor: Joe Smith` `Sample/Index2` uç nokta çağrıldığında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7040d-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="7040d-182">Aşağıdaki kod, `MyActionFilterAttribute` ve `AddHeaderAttribute` Razor sayfasını sayfasına uygular:</span><span class="sxs-lookup"><span data-stu-id="7040d-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7040d-183">Filtreler Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="7040d-184">Bunlar Razor sayfa modeline ya da genel olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="7040d-185">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7040d-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7040d-186">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7040d-187">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="7040d-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="7040d-188">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7040d-189">Bir denetleyici eyleminde bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="7040d-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="7040d-190">Filtre öznitelikleri, Razor sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="7040d-191">Bir denetleyici veya sayfada bir özniteliği kullanma Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="7040d-192">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler, Eylemler ve sayfalar için genel olarak Razor :</span><span class="sxs-lookup"><span data-stu-id="7040d-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="7040d-193">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="7040d-193">Default order of execution</span></span>

<span data-ttu-id="7040d-194">İşlem hattının belirli bir aşamasına ilişkin birden çok filtre olduğunda, kapsam varsayılan filtre yürütme sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="7040d-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7040d-195">Genel filtreler kapsayan sınıf filtreleri, bu da kapsayan Yöntem filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="7040d-196">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7040d-197">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="7040d-197">The filter sequence:</span></span>

* <span data-ttu-id="7040d-198">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7040d-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7040d-199">Denetleyicinin ve sayfa filtrelerinin *öncesindeki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="7040d-200">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7040d-201">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7040d-202">Denetleyicinin ve sayfa filtrelerinin *sonraki* kodu Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="7040d-203">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7040d-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7040d-204">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="7040d-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7040d-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="7040d-205">Sequence</span></span> | <span data-ttu-id="7040d-206">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7040d-206">Filter scope</span></span> | <span data-ttu-id="7040d-207">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7040d-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7040d-208">1</span><span class="sxs-lookup"><span data-stu-id="7040d-208">1</span></span> | <span data-ttu-id="7040d-209">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-210">2</span><span class="sxs-lookup"><span data-stu-id="7040d-210">2</span></span> | <span data-ttu-id="7040d-211">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="7040d-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="7040d-212">3</span><span class="sxs-lookup"><span data-stu-id="7040d-212">3</span></span> | <span data-ttu-id="7040d-213">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-214">4</span><span class="sxs-lookup"><span data-stu-id="7040d-214">4</span></span> | <span data-ttu-id="7040d-215">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7040d-216">5</span><span class="sxs-lookup"><span data-stu-id="7040d-216">5</span></span> | <span data-ttu-id="7040d-217">Denetleyici veya Razor sayfa</span><span class="sxs-lookup"><span data-stu-id="7040d-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7040d-218">6</span><span class="sxs-lookup"><span data-stu-id="7040d-218">6</span></span> | <span data-ttu-id="7040d-219">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="7040d-220">Denetleyici düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-220">Controller level filters</span></span>

<span data-ttu-id="7040d-221">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7040d-222">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="7040d-222">These methods:</span></span>

* <span data-ttu-id="7040d-223">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="7040d-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7040d-224">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7040d-225">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7040d-226">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7040d-227">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="7040d-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7040d-228">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7040d-229">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7040d-229">The `TestController`:</span></span>

* <span data-ttu-id="7040d-230">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="7040d-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7040d-231">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7040d-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="7040d-232">' A gitme `https://localhost:5001/Test2/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="7040d-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7040d-233">Denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini olarak ayarlar `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="7040d-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="7040d-234">Denetleyici düzeyi filtreleri metotlara uygulandıktan sonra çalıştırılacak şekilde ayarlanamaz. **not**</span><span class="sxs-lookup"><span data-stu-id="7040d-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="7040d-235">Sıra, sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7040d-235">Order is explained in the next section.</span></span>

<span data-ttu-id="7040d-236">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7040d-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7040d-237">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="7040d-237">Overriding the default order</span></span>

<span data-ttu-id="7040d-238">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7040d-239">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="7040d-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7040d-240">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="7040d-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7040d-241">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7040d-242">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7040d-243">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="7040d-244">Aşağıdaki denetleyicide iki eylem filtresini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="7040d-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7040d-245">İçinde genel bir filtre eklenir `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7040d-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="7040d-246">3 filtre aşağıdaki sırayla çalışır:</span><span class="sxs-lookup"><span data-stu-id="7040d-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="7040d-247">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="7040d-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7040d-248">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7040d-249">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7040d-250">Daha önce belirtildiği gibi, denetleyici düzeyi filtreleri [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) özelliğini `int.MinValue` yerleşik filtreler için olarak ayarlar, `Order` sıfır olmayan bir değere ayarlanmadığı sürece kapsam sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="7040d-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="7040d-251">Önceki kodda, `MySampleActionFilter` `MyAction2FilterAttribute` Denetleyici kapsamı olan, daha önce çalışması için genel kapsama sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7040d-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="7040d-252">`MyAction2FilterAttribute`Önce çalıştırmak için, sırayı şu şekilde ayarlayın `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7040d-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="7040d-253">Önce genel filtrenin çalışmasını sağlamak için şu `MySampleActionFilter` şekilde ayarlayın `Order` `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7040d-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7040d-254">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="7040d-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7040d-255">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7040d-256">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="7040d-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-257">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7040d-258">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7040d-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7040d-259">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7040d-260">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="7040d-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7040d-261">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="7040d-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7040d-262">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="7040d-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7040d-263">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="7040d-264">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7040d-264">Dependency injection</span></span>

<span data-ttu-id="7040d-265">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7040d-266">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7040d-267">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="7040d-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7040d-268">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="7040d-268">A type-activated filter means:</span></span>

* <span data-ttu-id="7040d-269">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7040d-269">An instance is created for each request.</span></span>
* <span data-ttu-id="7040d-270">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7040d-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7040d-271">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7040d-272">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="7040d-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7040d-273">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7040d-274">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="7040d-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7040d-275">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="7040d-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7040d-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="7040d-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7040d-277">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7040d-278">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-278">Loggers are available from DI.</span></span> <span data-ttu-id="7040d-279">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7040d-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7040d-280">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7040d-281">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="7040d-281">Logging added to filters:</span></span>

* <span data-ttu-id="7040d-282">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7040d-283">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="7040d-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7040d-284">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="7040d-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7040d-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7040d-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="7040d-286">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7040d-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7040d-287">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="7040d-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7040d-288">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="7040d-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7040d-289">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7040d-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="7040d-290">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="7040d-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7040d-291">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7040d-292">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7040d-293">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="7040d-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7040d-294">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7040d-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7040d-295">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="7040d-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7040d-296">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7040d-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7040d-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7040d-298">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7040d-299">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="7040d-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7040d-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7040d-300">TypeFilterAttribute</span></span>

<span data-ttu-id="7040d-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7040d-302">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="7040d-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7040d-303">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="7040d-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7040d-304">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7040d-305">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7040d-306">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7040d-307">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7040d-308">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7040d-309">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="7040d-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7040d-310">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7040d-311">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7040d-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7040d-312">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-312">Authorization filters</span></span>

<span data-ttu-id="7040d-313">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-313">Authorization filters:</span></span>

* <span data-ttu-id="7040d-314">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7040d-315">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-315">Control access to action methods.</span></span>
* <span data-ttu-id="7040d-316">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="7040d-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="7040d-317">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7040d-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7040d-318">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7040d-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7040d-319">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="7040d-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="7040d-320">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-320">Calls the authorization system.</span></span>
* <span data-ttu-id="7040d-321">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-321">Does not authorize requests.</span></span>

<span data-ttu-id="7040d-322">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="7040d-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7040d-323">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7040d-323">The exception will not be handled.</span></span>
* <span data-ttu-id="7040d-324">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7040d-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7040d-325">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="7040d-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7040d-326">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7040d-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7040d-327">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-327">Resource filters</span></span>

<span data-ttu-id="7040d-328">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-328">Resource filters:</span></span>

* <span data-ttu-id="7040d-329"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7040d-330">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="7040d-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7040d-331">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7040d-332">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7040d-333">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7040d-334">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-334">Resource filter examples:</span></span>

* <span data-ttu-id="7040d-335">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="7040d-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7040d-336">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7040d-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7040d-337">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="7040d-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7040d-338">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7040d-339">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-339">Action filters</span></span>

<span data-ttu-id="7040d-340">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7040d-341">Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7040d-342">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7040d-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7040d-343">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-343">Action filters:</span></span>

* <span data-ttu-id="7040d-344"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7040d-345">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="7040d-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7040d-346">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7040d-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7040d-347">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7040d-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-girişlerin bir eylem yöntemine okunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="7040d-349"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7040d-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7040d-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>- `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="7040d-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7040d-351">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="7040d-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7040d-352">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7040d-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7040d-353">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7040d-354">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7040d-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="7040d-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7040d-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="7040d-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7040d-357">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-357">Setting this property to null:</span></span>

  * <span data-ttu-id="7040d-358">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7040d-359">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7040d-360">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="7040d-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7040d-361">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="7040d-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7040d-362">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="7040d-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7040d-363">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="7040d-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7040d-364">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7040d-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7040d-365">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7040d-366">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7040d-366">Validate model state.</span></span>
* <span data-ttu-id="7040d-367">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="7040d-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-368">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="7040d-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7040d-369">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="7040d-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7040d-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7040d-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7040d-372">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7040d-373">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7040d-374">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7040d-375">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-375">Exception filters</span></span>

<span data-ttu-id="7040d-376">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-376">Exception filters:</span></span>

* <span data-ttu-id="7040d-377"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="7040d-378">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7040d-379">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7040d-380">Aşağıdaki kod özel durum filtresini sınar:</span><span class="sxs-lookup"><span data-stu-id="7040d-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="7040d-381">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-381">Exception filters:</span></span>

* <span data-ttu-id="7040d-382">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="7040d-382">Don't have before and after events.</span></span>
* <span data-ttu-id="7040d-383"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7040d-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7040d-384">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7040d-385">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="7040d-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7040d-386">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="7040d-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7040d-387">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7040d-387">This stops propagation of the exception.</span></span> <span data-ttu-id="7040d-388">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7040d-389">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-389">Only an action filter can do that.</span></span>

<span data-ttu-id="7040d-390">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-390">Exception filters:</span></span>

* <span data-ttu-id="7040d-391">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="7040d-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7040d-392">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="7040d-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7040d-393">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7040d-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7040d-394">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7040d-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7040d-395">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7040d-396">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7040d-397">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-397">Result filters</span></span>

<span data-ttu-id="7040d-398">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-398">Result filters:</span></span>

* <span data-ttu-id="7040d-399">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="7040d-399">Implement an interface:</span></span>
  * <span data-ttu-id="7040d-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7040d-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7040d-402">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="7040d-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7040d-403">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="7040d-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7040d-404">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7040d-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7040d-405">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="7040d-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7040d-406">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="7040d-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7040d-407">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7040d-408">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7040d-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7040d-409">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7040d-410">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="7040d-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="7040d-411">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7040d-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7040d-412">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7040d-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="7040d-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7040d-414">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="7040d-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7040d-415">İçinde bir özel durum üretiliyor `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="7040d-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="7040d-416">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önler.</span><span class="sxs-lookup"><span data-stu-id="7040d-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7040d-417">Başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7040d-418"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt muhtemelen istemciye zaten gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7040d-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="7040d-419">Yanıt istemciye zaten gönderildiyse, bu değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="7040d-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="7040d-420">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7040d-421">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7040d-422">`Exception`Null olarak ayarlanması bir özel durumu işler ve sonra işlem hattının daha sonra yeniden oluşturulmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7040d-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="7040d-423">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="7040d-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7040d-424">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="7040d-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7040d-425">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="7040d-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7040d-426">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="7040d-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7040d-427">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7040d-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7040d-428">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="7040d-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7040d-429">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="7040d-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7040d-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="7040d-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7040d-431">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="7040d-431">This includes action results produced by:</span></span>

* <span data-ttu-id="7040d-432">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7040d-433">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-433">Exception filters.</span></span>

<span data-ttu-id="7040d-434">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7040d-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7040d-435">IFilterFactory</span></span>

<span data-ttu-id="7040d-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7040d-437">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7040d-438">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="7040d-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7040d-439">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="7040d-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7040d-440">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7040d-441">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7040d-442">Filtre aşağıdaki kodda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="7040d-443">Önceki kodu [indirme örneğini](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)çalıştırarak test edin:</span><span class="sxs-lookup"><span data-stu-id="7040d-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="7040d-444">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="7040d-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7040d-445">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="7040d-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7040d-446">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="7040d-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7040d-447">**Yazar:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="7040d-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="7040d-448">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7040d-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7040d-449">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7040d-449">**internal:** `My header`</span></span>

<span data-ttu-id="7040d-450">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7040d-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7040d-451">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="7040d-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7040d-452">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="7040d-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7040d-453">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="7040d-454">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="7040d-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7040d-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7040d-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7040d-456">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7040d-457">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="7040d-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7040d-458">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="7040d-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7040d-459">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="7040d-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7040d-460">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="7040d-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7040d-461">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7040d-462">Ancak filtreler, çalışma zamanının bir parçası oldukları ve bu sayede bağlam ve yapılara erişimi olan ara yazılımlar farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="7040d-463">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7040d-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7040d-464">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7040d-465"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7040d-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7040d-466">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7040d-467">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="7040d-467">Next actions</span></span>

* <span data-ttu-id="7040d-468">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="7040d-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7040d-469">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="7040d-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7040d-470">[Kirk Larkabağı](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="7040d-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7040d-471">ASP.NET Core *Filtreler* , istek işleme ardışık düzeninde belirli aşamalardan önce veya sonra kod çalıştırılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7040d-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7040d-472">Yerleşik Filtreler şunları gibi görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="7040d-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7040d-473">Yetkilendirme (kullanıcının yetkili olmadığı kaynaklara erişimi önler).</span><span class="sxs-lookup"><span data-stu-id="7040d-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7040d-474">Yanıt önbelleğe alma (istek ardışık düzenini önbelleğe alınmış bir yanıt döndürecek şekilde döndürür).</span><span class="sxs-lookup"><span data-stu-id="7040d-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7040d-475">Çapraz kesme sorunlarını işlemek için özel filtreler oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7040d-476">Çapraz kesme sorunlarına örnek olarak hata işleme, önbelleğe alma, yapılandırma, yetkilendirme ve günlüğe kaydetme dahildir.</span><span class="sxs-lookup"><span data-stu-id="7040d-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7040d-477">Filtreler kodu çoğaltmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7040d-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="7040d-478">Örneğin, bir hata işleme özel durum filtresi hata işlemeyi birleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7040d-479">Bu belge Razor , görünümler içeren sayfalar, API denetleyicileri ve denetleyiciler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7040d-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="7040d-480">Örneği ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) .</span><span class="sxs-lookup"><span data-stu-id="7040d-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7040d-481">Filtreler nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="7040d-481">How filters work</span></span>

<span data-ttu-id="7040d-482">Filtreler, bazen *Filtre işlem hattı*olarak da adlandırılan *ASP.NET Core eylemi çağırma işlem hattı*içinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="7040d-483">Filtre işlem hattı çalıştırılacak eylemi ASP.NET Core seçtikten sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![İstek diğer ara yazılım, yönlendirme ara yazılımı, eylem seçimi ve ASP.NET Core eylemi çağırma Işlem hattı aracılığıyla işlenir.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7040d-486">Filtre türleri</span><span class="sxs-lookup"><span data-stu-id="7040d-486">Filter types</span></span>

<span data-ttu-id="7040d-487">Her filtre türü, filtre ardışık düzeninde farklı bir aşamada yürütülür:</span><span class="sxs-lookup"><span data-stu-id="7040d-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7040d-488">İlk olarak [Yetkilendirme filtreleri](#authorization-filters) çalışır ve kullanıcının istek için yetkilendirilip yetkilendirilmediğini tespit etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7040d-489">Yetkilendirme filtreleri, istek yetkisiz ise işlem hattı kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7040d-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="7040d-490">[Kaynak filtreleri](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7040d-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7040d-491">Yetkilendirmeden sonra çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7040d-491">Run after authorization.</span></span>  
  * <span data-ttu-id="7040d-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>Filtre işlem hattının geri kalanından önce kod çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7040d-493">Örneğin, `OnResourceExecuting` model bağlamadan önce kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="7040d-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>ardışık düzenin geri kalanı tamamlandıktan sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7040d-495">[Eylem filtreleri](#action-filters) , tek bir eylem yöntemi çağrıldıktan hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="7040d-496">Bir eyleme geçirilen bağımsız değişkenleri ve eylemden döndürülen sonucu işlemek için kullanılabilirler.</span><span class="sxs-lookup"><span data-stu-id="7040d-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="7040d-497">Eylem filtreleri sayfalarda **desteklenmez** Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7040d-498">[Özel durum filtreleri](#exception-filters) , yanıt gövdesine hiçbir şey yazılmadan önce oluşan işlenmemiş özel durumlara genel ilkeler uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="7040d-499">[Sonuç filtreleri](#result-filters) , tek tek eylem sonuçlarının yürütülmesinden hemen önce ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="7040d-500">Bunlar yalnızca eylem yöntemi başarıyla yürütüldüğünde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7040d-501">Bu değerler, görünüm veya biçimlendirici yürütmesinin yürütülmesi gereken mantık için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7040d-502">Aşağıdaki diyagramda filtre türlerinin filtre ardışık düzeninde nasıl etkileşimde bulunduğu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="7040d-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![İstek, Yetkilendirme filtreleri, kaynak filtreleri, model bağlama, eylem filtreleri, eylem yürütme ve eylem sonucu dönüştürme, özel durum filtreleri, sonuç filtreleri ve sonuç yürütmesi aracılığıyla işlenir.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7040d-505">Uygulama</span><span class="sxs-lookup"><span data-stu-id="7040d-505">Implementation</span></span>

<span data-ttu-id="7040d-506">Filtreler, farklı arabirim tanımları aracılığıyla hem zaman uyumlu hem de zaman uyumsuz uygulamaları destekler.</span><span class="sxs-lookup"><span data-stu-id="7040d-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7040d-507">Zaman uyumlu filtreler `On-Stage-Executing` `On-Stage-Executed` , ardışık düzen aşamasını () ve sonra kodu çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="7040d-508">Örneğin, `OnActionExecuting` eylem yöntemi çağrılmadan önce çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="7040d-509">`OnActionExecuted`, eylem yöntemi döndüğünde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7040d-510">Zaman uyumsuz filtreler bir `On-Stage-ExecutionAsync` yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7040d-511">Önceki kodda,, `SampleAsyncActionFilter` <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` Action metodunu yürüten bir () içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="7040d-512">Yöntemlerin her biri, `On-Stage-ExecutionAsync` `FilterType-ExecutionDelegate` filtrenin ardışık düzen aşamasını yürüten bir alır.</span><span class="sxs-lookup"><span data-stu-id="7040d-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7040d-513">Birden çok filtre aşaması</span><span class="sxs-lookup"><span data-stu-id="7040d-513">Multiple filter stages</span></span>

<span data-ttu-id="7040d-514">Birden çok filtre aşaması için arabirimler tek bir sınıfta uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7040d-515">Örneğin, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> sınıfı `IActionFilter` , `IResultFilter` ve zaman uyumsuz eşdeğerlerini uygular.</span><span class="sxs-lookup"><span data-stu-id="7040d-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="7040d-516">Her ikisini de **değil** , bir filtre arabiriminin zaman uyumlu veya zaman uyumsuz **sürümünü uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="7040d-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7040d-517">Çalışma zamanı öncelikle filtrenin zaman uyumsuz arabirimi uygulayıp uygulamadığını denetler ve bu durumda bunu çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7040d-518">Aksi takdirde, zaman uyumlu arabirimin Yöntem (ler) i çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7040d-519">Tek bir sınıfta hem zaman uyumsuz hem de zaman uyumlu arabirimler uygulanmışsa, yalnızca zaman uyumsuz yöntem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7040d-520">Soyut sınıfların kullanıldığı durumlarda <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , yalnızca zaman uyumlu yöntemleri veya her bir filtre türü için zaman uyumsuz yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="7040d-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7040d-521">Yerleşik filtre öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="7040d-521">Built-in filter attributes</span></span>

<span data-ttu-id="7040d-522">ASP.NET Core, alt sınıflanmış ve özelleştirilebilen yerleşik öznitelik tabanlı filtreler içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7040d-523">Örneğin, aşağıdaki sonuç filtresi yanıta bir üst bilgi ekler:</span><span class="sxs-lookup"><span data-stu-id="7040d-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-524">Öznitelikler, önceki örnekte gösterildiği gibi, filtrelerin bağımsız değişkenleri kabul etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="7040d-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7040d-525">' İ `AddHeaderAttribute` bir denetleyiciye veya eylem yöntemine uygulayın ve http üstbilgisinin adını ve değerini belirtin:</span><span class="sxs-lookup"><span data-stu-id="7040d-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="7040d-526">Filtre arabirimlerinden birkaçı, özel uygulamalar için temel sınıflar olarak kullanılabilecek karşılık gelen özniteliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="7040d-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7040d-527">Filtre öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7040d-528">Kapsamları ve yürütme sırasını filtrele</span><span class="sxs-lookup"><span data-stu-id="7040d-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="7040d-529">Üç *kapsamından*birindeki işlem hattına bir filtre eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7040d-530">Bir eylem üzerinde bir öznitelik kullanma.</span><span class="sxs-lookup"><span data-stu-id="7040d-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="7040d-531">Bir denetleyicide bir özniteliği kullanma.</span><span class="sxs-lookup"><span data-stu-id="7040d-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="7040d-532">Aşağıdaki kodda gösterildiği gibi tüm denetleyiciler ve eylemler için genel olarak:</span><span class="sxs-lookup"><span data-stu-id="7040d-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7040d-533">Yukarıdaki kod, [Mvcoptions. Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) koleksiyonunu kullanarak genel olarak üç filtre ekler.</span><span class="sxs-lookup"><span data-stu-id="7040d-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="7040d-534">Varsayılan yürütme sırası</span><span class="sxs-lookup"><span data-stu-id="7040d-534">Default order of execution</span></span>

<span data-ttu-id="7040d-535">*Aynı türde*birden çok filtre olduğunda kapsam, filtre yürütmenin varsayılan sırasını belirler.</span><span class="sxs-lookup"><span data-stu-id="7040d-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7040d-536">Genel filtreler saran sınıf filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-536">Global filters surround class filters.</span></span> <span data-ttu-id="7040d-537">Sınıf filtreleri surround yöntemi filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-537">Class filters surround method filters.</span></span>

<span data-ttu-id="7040d-538">Filtre iç içe geçme sonucu *olarak, filtrenin kodu,* *önceki* kodun ters sırasına göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7040d-539">Filtre sırası:</span><span class="sxs-lookup"><span data-stu-id="7040d-539">The filter sequence:</span></span>

* <span data-ttu-id="7040d-540">Genel filtrelerin *önceki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7040d-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7040d-541">Denetleyici filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="7040d-542">Eylem yöntemi filtrelerinden *önceki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7040d-543">Eylem yöntemi filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7040d-544">Denetleyici filtrelerinden *sonraki* kod.</span><span class="sxs-lookup"><span data-stu-id="7040d-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="7040d-545">Genel filtrelerin *sonraki* kodu.</span><span class="sxs-lookup"><span data-stu-id="7040d-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7040d-546">Zaman uyumlu eylem filtreleri için filtre yöntemlerinin çağrıldığı sırayı gösteren aşağıdaki örnek.</span><span class="sxs-lookup"><span data-stu-id="7040d-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7040d-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="7040d-547">Sequence</span></span> | <span data-ttu-id="7040d-548">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7040d-548">Filter scope</span></span> | <span data-ttu-id="7040d-549">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7040d-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7040d-550">1</span><span class="sxs-lookup"><span data-stu-id="7040d-550">1</span></span> | <span data-ttu-id="7040d-551">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-552">2</span><span class="sxs-lookup"><span data-stu-id="7040d-552">2</span></span> | <span data-ttu-id="7040d-553">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7040d-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-554">3</span><span class="sxs-lookup"><span data-stu-id="7040d-554">3</span></span> | <span data-ttu-id="7040d-555">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-556">4</span><span class="sxs-lookup"><span data-stu-id="7040d-556">4</span></span> | <span data-ttu-id="7040d-557">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7040d-558">5</span><span class="sxs-lookup"><span data-stu-id="7040d-558">5</span></span> | <span data-ttu-id="7040d-559">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7040d-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7040d-560">6</span><span class="sxs-lookup"><span data-stu-id="7040d-560">6</span></span> | <span data-ttu-id="7040d-561">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="7040d-562">Bu sıra şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="7040d-562">This sequence shows:</span></span>

* <span data-ttu-id="7040d-563">Yöntem filtresi, denetleyici filtresi içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="7040d-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="7040d-564">Denetleyici filtresi, genel filtrenin içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="7040d-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="7040d-565">Denetleyici ve Razor sayfa düzeyi filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="7040d-566">Temel sınıftan devralan her denetleyici <xref:Microsoft.AspNetCore.Mvc.Controller> [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. onactionexecutionasync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)ve [Controller. onactionyürütülmüş](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` yöntemlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="7040d-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7040d-567">Bu Yöntemler:</span><span class="sxs-lookup"><span data-stu-id="7040d-567">These methods:</span></span>

* <span data-ttu-id="7040d-568">Belirli bir eylem için çalışan filtreleri sarın.</span><span class="sxs-lookup"><span data-stu-id="7040d-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7040d-569">`OnActionExecuting`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7040d-570">`OnActionExecuted`tüm eylem filtrelerinden sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7040d-571">`OnActionExecutionAsync`, eylemin filtrelerinden herhangi birinin önüne çağırılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7040d-572">Eylem yöntemiyle çalıştıktan sonra filtredeki kod `next` .</span><span class="sxs-lookup"><span data-stu-id="7040d-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7040d-573">Örneğin, indirme örneğinde, `MySampleActionFilter` başlangıçta genel olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7040d-574">`TestController`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7040d-574">The `TestController`:</span></span>

* <span data-ttu-id="7040d-575">`SampleActionFilterAttribute` `[SampleActionFilter]` Eyleme () uygular `FilterTest2` .</span><span class="sxs-lookup"><span data-stu-id="7040d-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7040d-576">Geçersiz kılmalar `OnActionExecuting` ve `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="7040d-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7040d-577">' A gitme `https://localhost:5001/Test/FilterTest2` aşağıdaki kodu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="7040d-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7040d-578">RazorSayfalar için bkz. [ Razor filtre yöntemlerini geçersiz kılarak sayfa filtrelerini uygulama](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7040d-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7040d-579">Varsayılan sırayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="7040d-579">Overriding the default order</span></span>

<span data-ttu-id="7040d-580">Varsayılan yürütme sırası, uygulama tarafından geçersiz kılınabilir <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7040d-581">`IOrderedFilter`<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>yürütme sırasını tespit etmek için kapsama göre öncelik alan özelliği gösterir.</span><span class="sxs-lookup"><span data-stu-id="7040d-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7040d-582">Düşük değere sahip bir filtre `Order` :</span><span class="sxs-lookup"><span data-stu-id="7040d-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7040d-583">Daha yüksek değeri olan bir filtreden önce koddan *önce* kodu çalıştırır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7040d-584">Daha yüksek bir değere sahip bir filtrenin sonrasında koddan *sonra* çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7040d-585">`Order`Özelliği bir Oluşturucu parametresiyle ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="7040d-586">Yukarıdaki örnekte gösterilen 3 eylem filtresini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="7040d-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="7040d-587">`Order`Denetleyicinin ve genel filtrelerin özelliği sırasıyla 1 ve 2 olarak ayarlandıysa, yürütme sırası tersine çevrilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="7040d-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="7040d-588">Sequence</span></span> | <span data-ttu-id="7040d-589">Filtre kapsamı</span><span class="sxs-lookup"><span data-stu-id="7040d-589">Filter scope</span></span> | <span data-ttu-id="7040d-590">`Order`özelliði</span><span class="sxs-lookup"><span data-stu-id="7040d-590">`Order` property</span></span> | <span data-ttu-id="7040d-591">Filter yöntemi</span><span class="sxs-lookup"><span data-stu-id="7040d-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="7040d-592">1</span><span class="sxs-lookup"><span data-stu-id="7040d-592">1</span></span> | <span data-ttu-id="7040d-593">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-593">Method</span></span> | <span data-ttu-id="7040d-594">0</span><span class="sxs-lookup"><span data-stu-id="7040d-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7040d-595">2</span><span class="sxs-lookup"><span data-stu-id="7040d-595">2</span></span> | <span data-ttu-id="7040d-596">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7040d-596">Controller</span></span> | <span data-ttu-id="7040d-597">1</span><span class="sxs-lookup"><span data-stu-id="7040d-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7040d-598">3</span><span class="sxs-lookup"><span data-stu-id="7040d-598">3</span></span> | <span data-ttu-id="7040d-599">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-599">Global</span></span> | <span data-ttu-id="7040d-600">2</span><span class="sxs-lookup"><span data-stu-id="7040d-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7040d-601">4</span><span class="sxs-lookup"><span data-stu-id="7040d-601">4</span></span> | <span data-ttu-id="7040d-602">Genel</span><span class="sxs-lookup"><span data-stu-id="7040d-602">Global</span></span> | <span data-ttu-id="7040d-603">2</span><span class="sxs-lookup"><span data-stu-id="7040d-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7040d-604">5</span><span class="sxs-lookup"><span data-stu-id="7040d-604">5</span></span> | <span data-ttu-id="7040d-605">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="7040d-605">Controller</span></span> | <span data-ttu-id="7040d-606">1</span><span class="sxs-lookup"><span data-stu-id="7040d-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7040d-607">6</span><span class="sxs-lookup"><span data-stu-id="7040d-607">6</span></span> | <span data-ttu-id="7040d-608">Yöntem</span><span class="sxs-lookup"><span data-stu-id="7040d-608">Method</span></span> | <span data-ttu-id="7040d-609">0</span><span class="sxs-lookup"><span data-stu-id="7040d-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="7040d-610">`Order`Özelliği filtrelerin çalıştırılacağı sıra belirlenirken kapsamı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="7040d-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7040d-611">Filtreler sırasıyla sıraya göre sıralanır, ardından kapsam bölmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7040d-612">Yerleşik filtrelerin hepsi uygular `IOrderedFilter` ve varsayılan `Order` değeri 0 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7040d-613">Yerleşik filtreler için kapsam, `Order` sıfır olmayan bir değere ayarlanmadığı takdirde sıralamayı belirler.</span><span class="sxs-lookup"><span data-stu-id="7040d-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7040d-614">İptal ve kısa devre dışı</span><span class="sxs-lookup"><span data-stu-id="7040d-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7040d-615">Filtre işlem hattı, <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> filtre yöntemine sunulan parametrede özelliği ayarlanarak kısa devre yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7040d-616">Örneğin, aşağıdaki kaynak filtresi, ardışık düzenin geri kalanının yürütülmesini engeller:</span><span class="sxs-lookup"><span data-stu-id="7040d-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-617">Aşağıdaki kodda, `ShortCircuitingResourceFilter` ve `AddHeader` filtresi `SomeResource` Action metodunu hedefleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7040d-618">`ShortCircuitingResourceFilter`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="7040d-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7040d-619">İlk olarak bir kaynak filtresi olduğundan ve bir `AddHeader` eylem filtresiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7040d-620">Kısa süreli işlem hattının geri kalanı.</span><span class="sxs-lookup"><span data-stu-id="7040d-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7040d-621">Bu nedenle, `AddHeader` filtre eylem için hiçbir şekilde çalışmayacaktır `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="7040d-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7040d-622">Bu davranış, her iki filtre de eylem yöntemi düzeyinde uygulanırsa, ilk olarak çalıştırıldığında aynı olur `ShortCircuitingResourceFilter` .</span><span class="sxs-lookup"><span data-stu-id="7040d-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7040d-623">İlk olarak, `ShortCircuitingResourceFilter` filtre türü veya açıkça özelliğin kullanımı kullanılarak çalışır `Order` .</span><span class="sxs-lookup"><span data-stu-id="7040d-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="7040d-624">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7040d-624">Dependency injection</span></span>

<span data-ttu-id="7040d-625">Filtreler türe veya örneğe göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7040d-626">Bir örnek eklenirse, bu örnek her istek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7040d-627">Bir tür eklenirse, türü etkinleştirilmiş olur.</span><span class="sxs-lookup"><span data-stu-id="7040d-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7040d-628">Tür etkinleştirilmiş bir filtre şu anlama gelir:</span><span class="sxs-lookup"><span data-stu-id="7040d-628">A type-activated filter means:</span></span>

* <span data-ttu-id="7040d-629">Her istek için bir örnek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7040d-629">An instance is created for each request.</span></span>
* <span data-ttu-id="7040d-630">Herhangi bir Oluşturucu bağımlılığı [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7040d-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7040d-631">Öznitelik olarak uygulanan ve doğrudan denetleyici sınıflarına veya eylem yöntemlerine eklenen filtreler [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) tarafından sağlanmış Oluşturucu bağımlılıklarına sahip olamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7040d-632">Oluşturucu bağımlılıkları şu nedenle şu nedenle sağlanamaz:</span><span class="sxs-lookup"><span data-stu-id="7040d-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7040d-633">Özniteliklerin, uygulandıkları yerlerde, Oluşturucu parametreleri sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7040d-634">Bu, özniteliklerin nasıl çalıştığı konusunda bir kısıtlamadır.</span><span class="sxs-lookup"><span data-stu-id="7040d-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7040d-635">Aşağıdaki filtreler, dı tarafından belirtilen Oluşturucu bağımlılıklarını destekler:</span><span class="sxs-lookup"><span data-stu-id="7040d-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7040d-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>özniteliği üzerinde uygulandı.</span><span class="sxs-lookup"><span data-stu-id="7040d-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7040d-637">Önceki filtreler bir denetleyiciye veya eylem yöntemine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7040d-638">Günlükçüler, DI 'den alınabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-638">Loggers are available from DI.</span></span> <span data-ttu-id="7040d-639">Ancak, yalnızca günlüğe kaydetme amacıyla filtre oluşturmaktan ve kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="7040d-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7040d-640">[Yerleşik çerçeve günlüğü](xref:fundamentals/logging/index) genellikle günlüğe kaydetme için gerekenleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7040d-641">Filtrelere günlük eklendi:</span><span class="sxs-lookup"><span data-stu-id="7040d-641">Logging added to filters:</span></span>

* <span data-ttu-id="7040d-642">, İş etki alanı kaygılarını veya filtreye özgü davranışları odaklamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7040d-643">Eylemleri veya diğer çerçeve olaylarını günlüğe **içermemelidir** .</span><span class="sxs-lookup"><span data-stu-id="7040d-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7040d-644">Yerleşik filtreler günlük eylemleri ve çerçeve olayları.</span><span class="sxs-lookup"><span data-stu-id="7040d-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7040d-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7040d-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="7040d-646">Hizmet filtresi uygulama türleri ' de kaydedilir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7040d-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7040d-647">Bir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> filtrenin bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="7040d-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7040d-648">Aşağıdaki kod şunu gösterir `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="7040d-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7040d-649">Aşağıdaki kodda, `AddHeaderResultServiceFilter` dı kapsayıcısına eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7040d-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7040d-650">Aşağıdaki kodda, `ServiceFilter` özniteliği, bir filtrenin bir ÖRNEĞINI `AddHeaderResultServiceFilter` dı:</span><span class="sxs-lookup"><span data-stu-id="7040d-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7040d-651">Kullanırken `ServiceFilterAttribute` , [Servicefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7040d-652">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden *kullanılabilir olabileceğini gösteren* bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7040d-653">ASP.NET Core çalışma zamanı garanti etmez:</span><span class="sxs-lookup"><span data-stu-id="7040d-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7040d-654">Filtrenin tek bir örneğinin oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7040d-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7040d-655">Filtre, sonraki bir noktada dı kapsayıcısından yeniden istenmeyecek.</span><span class="sxs-lookup"><span data-stu-id="7040d-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7040d-656">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7040d-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7040d-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7040d-658">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7040d-659">`CreateInstance`belirtilen türü DI 'dan yükler.</span><span class="sxs-lookup"><span data-stu-id="7040d-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7040d-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7040d-660">TypeFilterAttribute</span></span>

<span data-ttu-id="7040d-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>benzerdir <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ancak türü doğrudan dı kapsayıcısından çözümlenmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7040d-662">Kullanarak türü başlatır <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="7040d-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7040d-663">`TypeFilterAttribute`Türler doğrudan dı kapsayıcısından çözümlenmediğinden:</span><span class="sxs-lookup"><span data-stu-id="7040d-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7040d-664">' İ kullanılarak başvurulan türlerin, `TypeFilterAttribute` dı kapsayıcısına kayıtlı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7040d-665">Bunların bağımlılıkları, dı kapsayıcısı tarafından yerine getirilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7040d-666">`TypeFilterAttribute`isteğe bağlı olarak tür için Oluşturucu bağımsız değişkenlerini kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7040d-667">Kullanırken `TypeFilterAttribute` , [Typefilterattribute. ıyeniden kullanılabilir](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7040d-668">Filtre örneğinin, içinde oluşturulduğu istek kapsamının dışında yeniden kullanılabilir *olabileceği* ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7040d-669">ASP.NET Core çalışma zamanı, filtrenin tek bir örneğinin oluşturulacağı garantisi vermez.</span><span class="sxs-lookup"><span data-stu-id="7040d-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7040d-670">Tek bir yaşam süresine sahip hizmetlere bağımlı olan bir filtreyle kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7040d-671">Aşağıdaki örnek kullanarak bir türe bağımsız değişkenlerin nasıl geçirileceğini göstermektedir `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="7040d-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7040d-672">Yetkilendirme filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-672">Authorization filters</span></span>

<span data-ttu-id="7040d-673">Yetkilendirme filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-673">Authorization filters:</span></span>

* <span data-ttu-id="7040d-674">, Filtre ardışık düzeninde ilk filtrelerin çalıştırılmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7040d-675">Eylem yöntemlerine erişimi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-675">Control access to action methods.</span></span>
* <span data-ttu-id="7040d-676">Bir Before yöntemi, ancak After yönteminden hiçbiri.</span><span class="sxs-lookup"><span data-stu-id="7040d-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="7040d-677">Özel Yetkilendirme filtreleri özel bir yetkilendirme çerçevesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7040d-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7040d-678">Özel bir filtre yazmak için yetkilendirme ilkelerini yapılandırmayı veya özel bir yetkilendirme ilkesi yazmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7040d-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7040d-679">Yerleşik yetkilendirme filtresi:</span><span class="sxs-lookup"><span data-stu-id="7040d-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="7040d-680">Yetkilendirme sistemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="7040d-680">Calls the authorization system.</span></span>
* <span data-ttu-id="7040d-681">İstekleri yetkilendirmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-681">Does not authorize requests.</span></span>

<span data-ttu-id="7040d-682">Yetkilendirme filtreleri içinde özel **durumlar atamayın:**</span><span class="sxs-lookup"><span data-stu-id="7040d-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7040d-683">Özel durum işlenmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7040d-683">The exception will not be handled.</span></span>
* <span data-ttu-id="7040d-684">Özel durum filtreleri özel durumu işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="7040d-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7040d-685">Bir yetkilendirme filtresinde özel durum oluştuğunda bir sınama vermeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="7040d-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7040d-686">[Yetkilendirme](xref:security/authorization/introduction)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7040d-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7040d-687">Kaynak filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-687">Resource filters</span></span>

<span data-ttu-id="7040d-688">Kaynak filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-688">Resource filters:</span></span>

* <span data-ttu-id="7040d-689"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7040d-690">Yürütme, filtre işlem hattının çoğunu sarmalar.</span><span class="sxs-lookup"><span data-stu-id="7040d-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7040d-691">Kaynak filtrelerinden önce yalnızca [Yetkilendirme filtreleri](#authorization-filters) çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7040d-692">Kaynak filtreleri, işlem hattının büyük bir yanındaki kısa devre için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="7040d-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7040d-693">Örneğin, bir önbelleğe alma filtresi, bir önbellek isabetinden ardışık düzen geri kalanından kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7040d-694">Kaynak filtresi örnekleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-694">Resource filter examples:</span></span>

* <span data-ttu-id="7040d-695">Daha önce gösterilen [kısa devre dışı kaynak filtresi](#short-circuiting-resource-filter) .</span><span class="sxs-lookup"><span data-stu-id="7040d-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7040d-696">[Disableformvaluemodelbindingattribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7040d-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7040d-697">Model bağlamanın form verilerine erişimini engeller.</span><span class="sxs-lookup"><span data-stu-id="7040d-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7040d-698">Form verilerinin belleğe okunmasını engellemek için büyük dosya yüklemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7040d-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7040d-699">Eylem filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7040d-700">Eylem filtreleri sayfalara **uygulanmaz** Razor .</span><span class="sxs-lookup"><span data-stu-id="7040d-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7040d-701">Sayfalar <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ve destekler <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7040d-702">Daha fazla bilgi için bkz. [ Razor sayfalar için filtre yöntemleri](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7040d-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7040d-703">Eylem filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-703">Action filters:</span></span>

* <span data-ttu-id="7040d-704"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>Ya da arabirimini uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7040d-705">Yürütmesinin, eylem yöntemlerinin yürütülmesi çevreler.</span><span class="sxs-lookup"><span data-stu-id="7040d-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7040d-706">Aşağıdaki kod bir örnek eylem filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7040d-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7040d-707">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7040d-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-bir eylem yöntemine yönelik girişlerin okunmalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="7040d-709"><xref:Microsoft.AspNetCore.Mvc.Controller>-denetleyici örneğinin işlenmesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="7040d-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7040d-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>- `Result` eylem yönteminin ve sonraki eylem filtrelerinin kısa devre dışı yürütülmesi ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="7040d-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7040d-711">Eylem yönteminde özel durum oluşturma:</span><span class="sxs-lookup"><span data-stu-id="7040d-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7040d-712">Sonraki filtrelerin çalıştırılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7040d-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7040d-713">Ayarından farklı olarak `Result` , başarılı bir sonuç yerine başarısızlık olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7040d-714">, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> `Controller` Ve `Result` ek olarak aşağıdaki özellikleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7040d-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-Eylem yürütmesi başka bir filtre tarafından kabul edilse true.</span><span class="sxs-lookup"><span data-stu-id="7040d-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7040d-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Eylem veya daha önce çalıştırılan eylem filtresi bir özel durum oluşturdu-null değil.</span><span class="sxs-lookup"><span data-stu-id="7040d-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7040d-717">Bu özellik null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-717">Setting this property to null:</span></span>

  * <span data-ttu-id="7040d-718">Özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7040d-719">`Result`eylem yönteminden döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7040d-720">Bir için `IAsyncActionFilter` bir çağrısı <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="7040d-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7040d-721">Sonraki eylem filtrelerini ve eylem yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="7040d-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7040d-722">`ActionExecutedContext` döndürür.</span><span class="sxs-lookup"><span data-stu-id="7040d-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7040d-723">Kısa devre dışı, <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> bir sonuç örneğine atayın ve çağırmayın `next` ( `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="7040d-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7040d-724">Çerçeve, alt sınıflı olabilecek bir Özet sağlar <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7040d-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7040d-725">`OnActionExecuting`Eylem filtresi şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7040d-726">Model durumunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7040d-726">Validate model state.</span></span>
* <span data-ttu-id="7040d-727">Durum geçersizse bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="7040d-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7040d-728">`OnActionExecuted`Yöntemi eylem yönteminden sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="7040d-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7040d-729">Ve, özelliği aracılığıyla eylemin sonuçlarını görebilir ve değiştirebilir <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .</span><span class="sxs-lookup"><span data-stu-id="7040d-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7040d-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>eylem yürütmesi başka bir filtre tarafından kabul edilse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7040d-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>eylem veya sonraki bir eylem filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7040d-732">`Exception`Null olarak ayarlanıyor:</span><span class="sxs-lookup"><span data-stu-id="7040d-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7040d-733">Bir özel durumu etkin bir şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7040d-734">`ActionExecutedContext.Result`eylem yönteminden normal olarak döndürülmüş gibi yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7040d-735">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-735">Exception filters</span></span>

<span data-ttu-id="7040d-736">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-736">Exception filters:</span></span>

* <span data-ttu-id="7040d-737"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="7040d-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="7040d-738">, Yaygın hata işleme ilkelerini uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7040d-739">Aşağıdaki örnek özel durum filtresi, uygulama geliştirmede olduğunda oluşan özel durumlar hakkındaki ayrıntıları görüntülemek için özel bir hata görünümü kullanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7040d-740">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-740">Exception filters:</span></span>

* <span data-ttu-id="7040d-741">Etkinlikden önceki ve sonraki olaylar yok.</span><span class="sxs-lookup"><span data-stu-id="7040d-741">Don't have before and after events.</span></span>
* <span data-ttu-id="7040d-742"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*>Veya uygulayın <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7040d-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7040d-743">RazorSayfa veya denetleyici oluşturma, [model bağlama](xref:mvc/models/model-binding), eylem filtreleri veya eylem yöntemlerinde oluşan işlenmemiş özel durumları işleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7040d-744">Kaynak filtrelerinde, sonuç filtrelerinde veya MVC sonuç yürütülürken oluşan özel **durumları yakalamayın** .</span><span class="sxs-lookup"><span data-stu-id="7040d-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7040d-745">Bir özel durumu işlemek için, <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> özelliğini `true` bir yanıt olarak ayarlayın veya yazın.</span><span class="sxs-lookup"><span data-stu-id="7040d-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7040d-746">Bu, özel durumun yayılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7040d-746">This stops propagation of the exception.</span></span> <span data-ttu-id="7040d-747">Özel durum filtresi bir özel durumu "başarılı" olarak açamaz.</span><span class="sxs-lookup"><span data-stu-id="7040d-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7040d-748">Yalnızca bir eylem filtresi bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-748">Only an action filter can do that.</span></span>

<span data-ttu-id="7040d-749">Özel durum filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-749">Exception filters:</span></span>

* <span data-ttu-id="7040d-750">Eylemler içinde oluşan özel durumları yakalamaya uygundur.</span><span class="sxs-lookup"><span data-stu-id="7040d-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7040d-751">, Hata işleme ara yazılımı olarak esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="7040d-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7040d-752">Özel durum işleme için ara yazılımı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="7040d-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7040d-753">Yalnızca hata işlemenin hangi eylem yöntemine göre *farklılık* gösteren özel durum filtrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7040d-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7040d-754">Örneğin, bir uygulama hem API uç noktaları hem de görünümler/HTML için eylem yöntemlerine sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7040d-755">API uç noktaları, hata bilgilerini JSON olarak döndürebilir, ancak görünüm tabanlı eylemler bir hata sayfasını HTML olarak döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7040d-756">Sonuç filtreleri</span><span class="sxs-lookup"><span data-stu-id="7040d-756">Result filters</span></span>

<span data-ttu-id="7040d-757">Sonuç filtreleri:</span><span class="sxs-lookup"><span data-stu-id="7040d-757">Result filters:</span></span>

* <span data-ttu-id="7040d-758">Arabirim uygulama:</span><span class="sxs-lookup"><span data-stu-id="7040d-758">Implement an interface:</span></span>
  * <span data-ttu-id="7040d-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7040d-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> veya <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7040d-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7040d-761">Yürütmesi, eylem sonuçlarının yürütülmesini çevreler.</span><span class="sxs-lookup"><span data-stu-id="7040d-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7040d-762">IResultFilter ve ıasyncresultfilter</span><span class="sxs-lookup"><span data-stu-id="7040d-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7040d-763">Aşağıdaki kod, bir HTTP üst bilgisi ekleyen bir sonuç filtresi gösterir:</span><span class="sxs-lookup"><span data-stu-id="7040d-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7040d-764">Yürütülen sonuç türü eyleme göre değişir.</span><span class="sxs-lookup"><span data-stu-id="7040d-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7040d-765">Bir görünüm döndüren bir eylem, çalıştırılmakta olan bir parçası olarak tüm Razor işlemlerini içerir <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="7040d-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7040d-766">Bir API yöntemi, sonucun yürütülmesinin bir parçası olarak bazı serileştirme işlemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7040d-767">[Eylem sonuçları](xref:mvc/controllers/actions)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="7040d-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7040d-768">Sonuç filtreleri yalnızca bir eylem veya eylem filtresi bir eylem sonucu üretirse yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7040d-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7040d-769">Şu durumlarda sonuç filtreleri yürütülmez:</span><span class="sxs-lookup"><span data-stu-id="7040d-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="7040d-770">Bir yetkilendirme filtresi veya kaynak filtresi, işlem hattı için kısa süreli olarak devre dışı.</span><span class="sxs-lookup"><span data-stu-id="7040d-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7040d-771">Bir özel durum filtresi, bir eylem sonucu üreterek özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="7040d-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7040d-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Yöntemi, olarak ayarlanarak eylem sonucunun ve sonraki sonuç filtrelerinin kısa devre yürütülmesine neden olabilir <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` .</span><span class="sxs-lookup"><span data-stu-id="7040d-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7040d-773">Boş bir yanıt oluşturmamaya kaçınmak için kısa devre dışı bırakıldığında yanıt nesnesine yazın.</span><span class="sxs-lookup"><span data-stu-id="7040d-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7040d-774">Bir özel durum oluşturmak için `IResultFilter.OnResultExecuting` şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="7040d-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="7040d-775">Eylem sonucunun ve sonraki filtrelerin yürütülmesini önleyin.</span><span class="sxs-lookup"><span data-stu-id="7040d-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7040d-776">Başarılı bir sonuç yerine hata olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7040d-777"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>Yöntem çalıştığında, yanıt zaten istemciye gönderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7040d-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="7040d-778">Yanıt istemciye zaten gönderildiyse, daha fazla değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="7040d-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="7040d-779">`ResultExecutedContext.Canceled``true`eylem sonucu yürütmesi başka bir filtre tarafından kabul edilen ise olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7040d-780">`ResultExecutedContext.Exception`eylem sonucu veya sonraki sonuç filtresi bir özel durum harekete geçirdi null olmayan bir değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="7040d-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7040d-781">`Exception`Null olarak ayarlanması bir özel durumu işler ve işlem hattının ilerleyen kısımlarında ASP.NET Core özel durumun yeniden oluşturulmasını önler.</span><span class="sxs-lookup"><span data-stu-id="7040d-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="7040d-782">Bir sonuç filtresinde özel durum işlenirken yanıta veri yazmanın güvenilir bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="7040d-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7040d-783">Bir eylem sonucu bir özel durum oluşturduğunda üstbilgiler istemciye temizleniyorsa, hata kodu göndermek için güvenilir bir mekanizma yoktur.</span><span class="sxs-lookup"><span data-stu-id="7040d-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7040d-784">Bir için bir <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> çağrısı, `await next` <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> sonraki sonuç filtrelerini ve eylem sonucunu yürütür.</span><span class="sxs-lookup"><span data-stu-id="7040d-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7040d-785">Kısa devre dışı, [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) olarak ayarlayın `true` ve şunu çağırmayın `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="7040d-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7040d-786">Çerçeve, alt sınıflı olabilecek bir Özet sağlar `ResultFilterAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7040d-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7040d-787">Daha önce gösterilen [Addheaderattribute](#add-header-attribute) sınıfı bir sonuç Filtresi özniteliği örneğidir.</span><span class="sxs-lookup"><span data-stu-id="7040d-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7040d-788">Ialwaysrunresultfilter ve ıasyncalwaysrunresultfilter</span><span class="sxs-lookup"><span data-stu-id="7040d-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7040d-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Ve <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> arabirimleri, <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> tüm eylem sonuçları için çalışan bir uygulama bildirir.</span><span class="sxs-lookup"><span data-stu-id="7040d-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7040d-790">Bu, tarafından oluşturulan eylem sonuçlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="7040d-790">This includes action results produced by:</span></span>

* <span data-ttu-id="7040d-791">Kısa devre olan Yetkilendirme filtreleri ve kaynak filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7040d-792">Özel durum filtreleri.</span><span class="sxs-lookup"><span data-stu-id="7040d-792">Exception filters.</span></span>

<span data-ttu-id="7040d-793">Örneğin, aşağıdaki filtre her zaman çalışır ve <xref:Microsoft.AspNetCore.Mvc.ObjectResult> içerik anlaşması başarısız olduğunda *422 olmayan bir varlık* durum kodu ile bir eylem sonucu () ayarlar:</span><span class="sxs-lookup"><span data-stu-id="7040d-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7040d-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7040d-794">IFilterFactory</span></span>

<span data-ttu-id="7040d-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7040d-796">Bu nedenle, bir `IFilterFactory` örnek, `IFilterMetadata` filtre ardışık düzeninde herhangi bir yerde örnek olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7040d-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7040d-797">Çalışma zamanı filtreyi çağırmayı hazırlarken, bir öğesine dönüştürmeyi dener `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="7040d-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7040d-798">Atama başarılı olursa, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> çağrılan örneği oluşturmak için yöntemi çağırılır `IFilterMetadata` .</span><span class="sxs-lookup"><span data-stu-id="7040d-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7040d-799">Bu, tam filtre işlem hattının uygulama başladığında açıkça ayarlanması gerektiğinden esnek bir tasarım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7040d-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7040d-800">`IFilterFactory`, filtre oluşturmaya yönelik başka bir yaklaşım olarak özel öznitelik uygulamaları kullanılarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7040d-801">Önceki kod, [indirme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)çalıştırılarak test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="7040d-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="7040d-802">F12 geliştirici araçlarını çağırın.</span><span class="sxs-lookup"><span data-stu-id="7040d-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7040d-803">`https://localhost:5001/Sample/HeaderWithFactory` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="7040d-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7040d-804">F12 geliştirici araçları, örnek kod tarafından eklenen aşağıdaki yanıt üstbilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="7040d-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7040d-805">**Yazar:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="7040d-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="7040d-806">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7040d-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7040d-807">**iç:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7040d-807">**internal:** `My header`</span></span>

<span data-ttu-id="7040d-808">Yukarıdaki kod, **iç:** `My header` yanıt üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7040d-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7040d-809">Öznitelik üzerinde IFilterFactory uygulandı</span><span class="sxs-lookup"><span data-stu-id="7040d-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7040d-810">Uygulayan filtreler `IFilterFactory` Şu filtreler için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="7040d-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7040d-811">Parametre geçirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7040d-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="7040d-812">DI tarafından doldurulması gereken Oluşturucu bağımlılıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="7040d-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7040d-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>uygular <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="7040d-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7040d-814">`IFilterFactory`<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>örnek oluşturma yöntemini gösterir <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="7040d-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7040d-815">`CreateInstance`belirtilen türü hizmetler kapsayıcısından (dı) yükler.</span><span class="sxs-lookup"><span data-stu-id="7040d-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7040d-816">Aşağıdaki kod, uygulamak için üç yaklaşımda gösterilmiştir `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="7040d-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7040d-817">Yukarıdaki kodda, yöntemi ile dekorasyon, `[SampleActionFilter]` uygulamak için tercih edilen yaklaşımdır `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="7040d-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7040d-818">Filtre ardışık düzeninde ara yazılım kullanma</span><span class="sxs-lookup"><span data-stu-id="7040d-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7040d-819">Kaynak filtreleri, işlem hattında daha sonra gelen her şeyin yürütülmesini çevreleyecek olan [Ara yazılım](xref:fundamentals/middleware/index) gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7040d-820">Ancak filtreler, ASP.NET Core çalışma zamanının bir parçası olan ara yazılımlar dışında farklılık gösterir, bu da ASP.NET Core bağlamına ve yapılarına erişime sahip oldukları anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="7040d-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="7040d-821">Ara yazılımı bir filtre olarak kullanmak için, `Configure` filtre ardışık düzenine eklenecek olan ara yazılımı belirten bir yöntemi olan bir tür oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7040d-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7040d-822">Aşağıdaki örnek, bir istek için geçerli kültürü oluşturmak üzere yerelleştirme ara yazılımını kullanır:</span><span class="sxs-lookup"><span data-stu-id="7040d-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7040d-823"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>Ara yazılımını çalıştırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7040d-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7040d-824">Ara yazılım filtreleri, filtre işlem hattının aynı aşamasında, model bağlamadan önce ve işlem hattının geri kalanı ile kaynak filtreleri olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="7040d-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7040d-825">Sonraki eylemler</span><span class="sxs-lookup"><span data-stu-id="7040d-825">Next actions</span></span>

* <span data-ttu-id="7040d-826">[ Razor Sayfalar için filtre yöntemlerine](xref:razor-pages/filter)bakın.</span><span class="sxs-lookup"><span data-stu-id="7040d-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7040d-827">Filtrelerle denemek için [GitHub örneğini indirin, test edin ve değiştirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="7040d-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
