---
title: RazorASP.NET Core Içindeki uygulama bölümleriyle denetleyicileri, görünümleri, sayfaları ve daha fazlasını paylaşma
author: rick-anderson
description: RazorASP.NET Core Içindeki uygulama bölümleriyle denetleyicileri, görünümü, sayfaları ve daha fazlasını paylaşma
ms.author: riande
ms.date: 11/11/2019
no-loc:
- appsettings.json
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061190"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>Uygulama parçalarıyla denetleyiciler, görünümler, Razor Sayfalar ve daha fazlasını paylaşma

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır. Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> bir uygulama bölümüdür. `AssemblyPart` derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.

[Özellik sağlayıcıları](#fp) , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur. Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır. Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz. Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor Sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (dll) paylaşabilirsiniz. Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.

ASP.NET Core uygulamalar, içindeki özellikleri yükler <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Sınıfı, bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.

## <a name="load-aspnet-core-features"></a>ASP.NET Core özellikleri yükle

<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Uygulama parçalarını ve özellik sağlayıcılarını izler. `ApplicationPartManager` Şu şekilde yapılandırılır `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Aşağıdaki kod, kullanarak yapılandırma için alternatif bir yaklaşım `ApplicationPartManager` sağlar `AssemblyPart` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Önceki iki kod örneği `SharedController` bir derlemeden yüklenir. `SharedController`Uygulamanın projesinde değil. Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) örneği indirmesi.

### <a name="include-views"></a>Görünümleri dahil et

Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.

### <a name="prevent-loading-resources"></a>Kaynakları yüklemeyi engelle

Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>Kullanılabilir kaynakları gizlemek veya saklamak için koleksiyonun üyelerini ekleyin veya kaldırın. Koleksiyondaki girişlerin sırası `ApplicationParts` önemli değildir. Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın. Örneğin, öğesini `ApplicationPartManager` çağırmadan önce öğesini yapılandırın `AddControllersAsServices` . `Remove` `ApplicationParts` Kaynağı kaldırmak için koleksiyonda çağrı yapın.

, `ApplicationPartManager` Şunlar için parçalar içerir:

* Uygulamanın derlemesi ve bağımlı derlemeleri.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Özellik sağlayıcıları

Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar. Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor Compiledıtemfeatureprovider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Özellik sağlayıcıları öğesinden devralınır <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , burada `T` özelliğin türüdür. Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir. İçindeki özellik sağlayıcılarının sırası `ApplicationPartManager.FeatureProviders` çalışma süresi davranışından etkileyebilir. Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.

### <a name="display-available-features"></a>Kullanılabilir özellikleri görüntüle

Bir uygulama için kullanılabilen özellikler, bir `ApplicationPartManager` [bağımlılık ekleme](../../fundamentals/dependency-injection.md)isteği isteyerek numaralandırılabilir:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Yükleme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:

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

## <a name="discovery-in-application-parts"></a>Uygulama bölümlerinde bulma

HTTP 404 hataları, uygulama parçalarıyla geliştirme sırasında sık karşılaşılan bir durumdur. Bu hatalar genellikle uygulamaların bölümlerinin nasıl keşfedildiği konusunda önemli bir gereksinimin eksik olmasından kaynaklanır. Uygulamanız bir HTTP 404 hatası döndürürse, aşağıdaki gereksinimlerin karşılandığından emin olun:

* `applicationName`Ayarın, bulma için kullanılan kök derlemeye ayarlanması gerekir. Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.
* Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir. Başvuru doğrudan veya geçişli olabilir.
* Kök derlemenin Web SDK 'sına başvurması gerekiyor. Çerçevede, öznitelikleri bulma için kullanılan kök derlemeye damgalayan mantık vardır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır. Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) bir uygulama bölümüdür. `AssemblyPart` derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.

*Özellik sağlayıcıları* , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur. Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır. Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz. Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor Sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir derlemeyi (dll) paylaşabilirsiniz. Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.

ASP.NET Core uygulamalar, içindeki özellikleri yükler <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Sınıfı, bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.

## <a name="load-aspnet-core-features"></a>ASP.NET Core özellikleri yükle

`ApplicationPart` `AssemblyPart` ASP.NET Core özelliklerini (denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Uygulama parçalarını ve özellik sağlayıcılarını izler. `ApplicationPartManager` Şu şekilde yapılandırılır `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Aşağıdaki kod, kullanarak yapılandırma için alternatif bir yaklaşım `ApplicationPartManager` sağlar `AssemblyPart` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Önceki iki kod örneği `SharedController` bir derlemeden yüklenir. `SharedController`Uygulamanın projesinde değil. Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) örneği indirmesi.

### <a name="include-views"></a>Görünümleri dahil et

Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.

### <a name="prevent-loading-resources"></a>Kaynakları yüklemeyi engelle

Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>Kullanılabilir kaynakları gizlemek veya saklamak için koleksiyonun üyelerini ekleyin veya kaldırın. Koleksiyondaki girişlerin sırası `ApplicationParts` önemli değildir. Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın. Örneğin, öğesini `ApplicationPartManager` çağırmadan önce öğesini yapılandırın `AddControllersAsServices` . `Remove` `ApplicationParts` Kaynağı kaldırmak için koleksiyonda çağrı yapın.

Aşağıdaki kod <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> uygulamadan kaldırmak için kullanır `MyDependentLibrary` : [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

, `ApplicationPartManager` Şunlar için parçalar içerir:

* Uygulamanın derlemesi ve bağımlı derlemeleri.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Uygulama özelliği sağlayıcıları

Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar. Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:

* [Denetleyiciler](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Etiket Yardımcıları](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Bileşenleri görüntüle](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Özellik sağlayıcıları öğesinden devralınır <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , burada `T` özelliğin türüdür. Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir. İçindeki özellik sağlayıcılarının sırası `ApplicationPartManager.FeatureProviders` çalışma süresi davranışından etkileyebilir. Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.

### <a name="display-available-features"></a>Kullanılabilir özellikleri görüntüle

Bir uygulama için kullanılabilen özellikler, bir `ApplicationPartManager` [bağımlılık ekleme](../../fundamentals/dependency-injection.md)isteği isteyerek numaralandırılabilir:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Yükleme örneği](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) , uygulama özelliklerini göstermek için yukarıdaki kodu kullanır:

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

## <a name="discovery-in-application-parts"></a>Uygulama bölümlerinde bulma

HTTP 404 hataları, uygulama parçalarıyla geliştirme sırasında sık karşılaşılan bir durumdur. Bu hatalar genellikle uygulamaların bölümlerinin nasıl keşfedildiği konusunda önemli bir gereksinimin eksik olmasından kaynaklanır. Uygulamanız bir HTTP 404 hatası döndürürse, aşağıdaki gereksinimlerin karşılandığından emin olun:

* `applicationName`Ayarın, bulma için kullanılan kök derlemeye ayarlanması gerekir. Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.
* Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir. Başvuru doğrudan veya geçişli olabilir.
* Kök derlemenin Web SDK 'sına başvurması gerekiyor.
  * ASP.NET Core Framework 'te, bulma için kullanılan kök derlemeye öznitelikleri damgalayan özel yapı mantığı vardır.

::: moniker-end
