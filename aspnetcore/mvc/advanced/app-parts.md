---
title: RazorASP.NET Core Içindeki uygulama bölümleriyle denetleyicileri, görünümleri, sayfaları ve daha fazlasını paylaşma
author: rick-anderson
description: RazorASP.NET Core Içindeki uygulama bölümleriyle denetleyicileri, görünümü, sayfaları ve daha fazlasını paylaşma
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061190"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="233c4-103">Uygulama parçalarıyla denetleyiciler, görünümler, Razor Sayfalar ve daha fazlasını paylaşma</span><span class="sxs-lookup"><span data-stu-id="233c4-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="233c4-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="233c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="233c4-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="233c4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="233c4-106">*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır.</span><span class="sxs-lookup"><span data-stu-id="233c4-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="233c4-107">Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="233c4-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="233c4-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> bir uygulama bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="233c4-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="233c4-109">`AssemblyPart` derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.</span><span class="sxs-lookup"><span data-stu-id="233c4-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="233c4-110">[Özellik sağlayıcıları](#fp) , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur.</span><span class="sxs-lookup"><span data-stu-id="233c4-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="233c4-111">Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="233c4-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="233c4-112">Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="233c4-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="233c4-113">Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor Sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (dll) paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="233c4-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="233c4-114">Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="233c4-115">ASP.NET Core uygulamalar, içindeki özellikleri yükler <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="233c4-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="233c4-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Sınıfı, bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="233c4-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="233c4-117">ASP.NET Core özellikleri yükle</span><span class="sxs-lookup"><span data-stu-id="233c4-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="233c4-118"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="233c4-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="233c4-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Uygulama parçalarını ve özellik sağlayıcılarını izler.</span><span class="sxs-lookup"><span data-stu-id="233c4-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="233c4-120">`ApplicationPartManager` Şu şekilde yapılandırılır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="233c4-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="233c4-121">Aşağıdaki kod, kullanarak yapılandırma için alternatif bir yaklaşım `ApplicationPartManager` sağlar `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="233c4-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="233c4-122">Önceki iki kod örneği `SharedController` bir derlemeden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="233c4-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="233c4-123">`SharedController`Uygulamanın projesinde değil.</span><span class="sxs-lookup"><span data-stu-id="233c4-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="233c4-124">Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) örneği indirmesi.</span><span class="sxs-lookup"><span data-stu-id="233c4-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="233c4-125">Görünümleri dahil et</span><span class="sxs-lookup"><span data-stu-id="233c4-125">Include views</span></span>

<span data-ttu-id="233c4-126">Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.</span><span class="sxs-lookup"><span data-stu-id="233c4-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="233c4-127">Kaynakları yüklemeyi engelle</span><span class="sxs-lookup"><span data-stu-id="233c4-127">Prevent loading resources</span></span>

<span data-ttu-id="233c4-128">Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="233c4-129"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts>Kullanılabilir kaynakları gizlemek veya saklamak için koleksiyonun üyelerini ekleyin veya kaldırın.</span><span class="sxs-lookup"><span data-stu-id="233c4-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="233c4-130">Koleksiyondaki girişlerin sırası `ApplicationParts` önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="233c4-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="233c4-131">Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="233c4-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="233c4-132">Örneğin, öğesini `ApplicationPartManager` çağırmadan önce öğesini yapılandırın `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="233c4-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="233c4-133">`Remove` `ApplicationParts` Kaynağı kaldırmak için koleksiyonda çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="233c4-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="233c4-134">, `ApplicationPartManager` Şunlar için parçalar içerir:</span><span class="sxs-lookup"><span data-stu-id="233c4-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="233c4-135">Uygulamanın derlemesi ve bağımlı derlemeleri.</span><span class="sxs-lookup"><span data-stu-id="233c4-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="233c4-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="233c4-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="233c4-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="233c4-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="233c4-138">Özellik sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="233c4-138">Feature providers</span></span>

<span data-ttu-id="233c4-139">Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar.</span><span class="sxs-lookup"><span data-stu-id="233c4-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="233c4-140">Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:</span><span class="sxs-lookup"><span data-stu-id="233c4-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="233c4-141">`internal class`[ Razor Compiledıtemfeatureprovider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="233c4-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="233c4-142">Özellik sağlayıcıları öğesinden devralınır <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , burada `T` özelliğin türüdür.</span><span class="sxs-lookup"><span data-stu-id="233c4-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="233c4-143">Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="233c4-144">İçindeki özellik sağlayıcılarının sırası `ApplicationPartManager.FeatureProviders` çalışma süresi davranışından etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="233c4-145">Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="233c4-146">Kullanılabilir özellikleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="233c4-146">Display available features</span></span>

<span data-ttu-id="233c4-147">Bir uygulama için kullanılabilen özellikler, bir `ApplicationPartManager` [bağımlılık ekleme](../../fundamentals/dependency-injection.md)isteği isteyerek numaralandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="233c4-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="233c4-148">[Yükleme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:</span><span class="sxs-lookup"><span data-stu-id="233c4-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="233c4-149">Uygulama bölümlerinde bulma</span><span class="sxs-lookup"><span data-stu-id="233c4-149">Discovery in application parts</span></span>

<span data-ttu-id="233c4-150">HTTP 404 hataları, uygulama parçalarıyla geliştirme sırasında sık karşılaşılan bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="233c4-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="233c4-151">Bu hatalar genellikle uygulamaların bölümlerinin nasıl keşfedildiği konusunda önemli bir gereksinimin eksik olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="233c4-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="233c4-152">Uygulamanız bir HTTP 404 hatası döndürürse, aşağıdaki gereksinimlerin karşılandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="233c4-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="233c4-153">`applicationName`Ayarın, bulma için kullanılan kök derlemeye ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="233c4-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="233c4-154">Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.</span><span class="sxs-lookup"><span data-stu-id="233c4-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="233c4-155">Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="233c4-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="233c4-156">Başvuru doğrudan veya geçişli olabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="233c4-157">Kök derlemenin Web SDK 'sına başvurması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="233c4-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="233c4-158">Çerçevede, öznitelikleri bulma için kullanılan kök derlemeye damgalayan mantık vardır.</span><span class="sxs-lookup"><span data-stu-id="233c4-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="233c4-159">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="233c4-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="233c4-160">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="233c4-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="233c4-161">*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır.</span><span class="sxs-lookup"><span data-stu-id="233c4-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="233c4-162">Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="233c4-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="233c4-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) bir uygulama bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="233c4-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="233c4-164">`AssemblyPart` derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.</span><span class="sxs-lookup"><span data-stu-id="233c4-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="233c4-165">*Özellik sağlayıcıları* , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur.</span><span class="sxs-lookup"><span data-stu-id="233c4-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="233c4-166">Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="233c4-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="233c4-167">Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="233c4-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="233c4-168">Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor Sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (dll) paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="233c4-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="233c4-169">Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="233c4-170">ASP.NET Core uygulamalar, içindeki özellikleri yükler <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="233c4-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="233c4-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Sınıfı, bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="233c4-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="233c4-172">ASP.NET Core özellikleri yükle</span><span class="sxs-lookup"><span data-stu-id="233c4-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="233c4-173">`ApplicationPart` `AssemblyPart` ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="233c4-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="233c4-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Uygulama parçalarını ve özellik sağlayıcılarını izler.</span><span class="sxs-lookup"><span data-stu-id="233c4-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="233c4-175">`ApplicationPartManager` Şu şekilde yapılandırılır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="233c4-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="233c4-176">Aşağıdaki kod, kullanarak yapılandırma için alternatif bir yaklaşım `ApplicationPartManager` sağlar `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="233c4-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="233c4-177">Önceki iki kod örneği `SharedController` bir derlemeden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="233c4-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="233c4-178">`SharedController`Uygulamanın projesinde değil.</span><span class="sxs-lookup"><span data-stu-id="233c4-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="233c4-179">Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) örneği indirmesi.</span><span class="sxs-lookup"><span data-stu-id="233c4-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="233c4-180">Görünümleri dahil et</span><span class="sxs-lookup"><span data-stu-id="233c4-180">Include views</span></span>

<span data-ttu-id="233c4-181">Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.</span><span class="sxs-lookup"><span data-stu-id="233c4-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="233c4-182">Kaynakları yüklemeyi engelle</span><span class="sxs-lookup"><span data-stu-id="233c4-182">Prevent loading resources</span></span>

<span data-ttu-id="233c4-183">Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="233c4-184"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts>Kullanılabilir kaynakları gizlemek veya saklamak için koleksiyonun üyelerini ekleyin veya kaldırın.</span><span class="sxs-lookup"><span data-stu-id="233c4-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="233c4-185">Koleksiyondaki girişlerin sırası `ApplicationParts` önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="233c4-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="233c4-186">Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="233c4-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="233c4-187">Örneğin, öğesini `ApplicationPartManager` çağırmadan önce öğesini yapılandırın `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="233c4-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="233c4-188">`Remove` `ApplicationParts` Kaynağı kaldırmak için koleksiyonda çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="233c4-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="233c4-189">Aşağıdaki kod <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> uygulamadan kaldırmak için kullanır `MyDependentLibrary` : [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="233c4-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="233c4-190">, `ApplicationPartManager` Şunlar için parçalar içerir:</span><span class="sxs-lookup"><span data-stu-id="233c4-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="233c4-191">Uygulamanın derlemesi ve bağımlı derlemeleri.</span><span class="sxs-lookup"><span data-stu-id="233c4-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="233c4-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="233c4-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="233c4-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="233c4-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="233c4-194">Uygulama özelliği sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="233c4-194">Application feature providers</span></span>

<span data-ttu-id="233c4-195">Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar.</span><span class="sxs-lookup"><span data-stu-id="233c4-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="233c4-196">Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:</span><span class="sxs-lookup"><span data-stu-id="233c4-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="233c4-197">Denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="233c4-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="233c4-198">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="233c4-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="233c4-199">Bileşenleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="233c4-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="233c4-200">Özellik sağlayıcıları öğesinden devralınır <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , burada `T` özelliğin türüdür.</span><span class="sxs-lookup"><span data-stu-id="233c4-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="233c4-201">Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="233c4-202">İçindeki özellik sağlayıcılarının sırası `ApplicationPartManager.FeatureProviders` çalışma süresi davranışından etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="233c4-203">Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="233c4-204">Kullanılabilir özellikleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="233c4-204">Display available features</span></span>

<span data-ttu-id="233c4-205">Bir uygulama için kullanılabilen özellikler, bir `ApplicationPartManager` [bağımlılık ekleme](../../fundamentals/dependency-injection.md)isteği isteyerek numaralandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="233c4-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="233c4-206">[Yükleme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:</span><span class="sxs-lookup"><span data-stu-id="233c4-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="233c4-207">Uygulama bölümlerinde bulma</span><span class="sxs-lookup"><span data-stu-id="233c4-207">Discovery in application parts</span></span>

<span data-ttu-id="233c4-208">HTTP 404 hataları, uygulama parçalarıyla geliştirme sırasında sık karşılaşılan bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="233c4-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="233c4-209">Bu hatalar genellikle uygulamaların bölümlerinin nasıl keşfedildiği konusunda önemli bir gereksinimin eksik olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="233c4-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="233c4-210">Uygulamanız bir HTTP 404 hatası döndürürse, aşağıdaki gereksinimlerin karşılandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="233c4-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="233c4-211">`applicationName`Ayarın, bulma için kullanılan kök derlemeye ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="233c4-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="233c4-212">Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.</span><span class="sxs-lookup"><span data-stu-id="233c4-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="233c4-213">Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="233c4-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="233c4-214">Başvuru doğrudan veya geçişli olabilir.</span><span class="sxs-lookup"><span data-stu-id="233c4-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="233c4-215">Kök derlemenin Web SDK 'sına başvurması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="233c4-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="233c4-216">ASP.NET Core Framework 'te, bulma için kullanılan kök derlemeye öznitelikleri damgalayan özel yapı mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="233c4-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
