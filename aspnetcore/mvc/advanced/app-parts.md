---
title: ASP.NET Core içindeki uygulama bölümleriyle denetleyicileri Razor , görünümleri, sayfaları ve daha fazlasını paylaşma
author: rick-anderson
description: ASP.NET Core içindeki uygulama bölümleriyle denetleyicileri Razor , görünümü, sayfaları ve daha fazlasını paylaşma
ms.author: riande
ms.date: 11/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 68991a3df5e09b63dc52bdadae55f055a721ad3c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774411"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Uygulama parçalarıyla denetleyiciler, görünümler Razor , sayfalar ve daha fazlasını paylaşma

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır. Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>bir uygulama bölümüdür. `AssemblyPart`derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.

[Özellik sağlayıcıları](#fp) , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur. Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır. Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz. Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir DERLEMEYI (dll) paylaşabilirsiniz. Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.

ASP.NET Core uygulamalar, içindeki <xref:System.Web.WebPages.ApplicationPart>özellikleri yükler. Sınıfı <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> , bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.

## <a name="load-aspnet-core-features"></a>ASP.NET Core özellikleri yükle

ASP.NET Core özelliklerini <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> ( <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın. Uygulama <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> parçalarını ve özellik sağlayıcılarını izler. `ApplicationPartManager`Şu şekilde yapılandırılır `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Aşağıdaki kod, kullanarak `ApplicationPartManager` `AssemblyPart`yapılandırma için alternatif bir yaklaşım sağlar:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Önceki iki kod örneği bir derlemeden yüklenir `SharedController` . `SharedController` Uygulamanın projesinde değil. Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) örneği indirmesi.

### <a name="include-views"></a>Görünümleri dahil et

Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.

### <a name="prevent-loading-resources"></a>Kaynakları yüklemeyi engelle

Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir. Kullanılabilir kaynakları gizlemek veya saklamak için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> koleksiyonun üyelerini ekleyin veya kaldırın. `ApplicationParts` Koleksiyondaki girişlerin sırası önemli değildir. Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın. Örneğin, öğesini çağırmadan `ApplicationPartManager` `AddControllersAsServices`önce öğesini yapılandırın. Kaynağı `Remove` kaldırmak için `ApplicationParts` koleksiyonda çağrı yapın.

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
* `internal class`[RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Özellik sağlayıcıları öğesinden <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>devralınır, burada `T` özelliğin türüdür. Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir. İçindeki `ApplicationPartManager.FeatureProviders` özellik sağlayıcılarının sırası çalışma süresi davranışından etkileyebilir. Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.

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

* Ayarın `applicationName` , bulma için kullanılan kök derlemeye ayarlanması gerekir. Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.
* Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir. Başvuru doğrudan veya geçişli olabilir.
* Kök derlemenin Web SDK 'sına başvurması gerekiyor. Çerçevede, öznitelikleri bulma için kullanılan kök derlemeye damgalayan mantık vardır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

*Uygulama bölümü* , bir uygulamanın kaynakları üzerinde soyutlamadır. Uygulama bölümleri ASP.NET Core denetleyicileri bulmasına, bileşenleri, etiket yardımcılarını, Razor sayfaları, Razor derleme kaynaklarını ve daha fazlasını bulmasına olanak tanır. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) bir uygulama bölümüdür. `AssemblyPart`derleme başvurusunu kapsüller ve türleri ve derleme başvurularını ortaya koyar.

*Özellik sağlayıcıları* , uygulama bölümleriyle birlikte çalışarak bir ASP.NET Core uygulamasının özelliklerini doldurur. Uygulama bölümleri için ana kullanım örneği, bir derlemeyi, bir derlemeden ASP.NET Core özellikleri bulacak (ya da yüklemeden kaçınacak) şekilde yapılandırmaktır. Örneğin, birden çok uygulama arasında ortak işlevselliği paylaşmak isteyebilirsiniz. Uygulama parçalarını kullanarak, çeşitli uygulamalarla denetleyiciler, görünümler, Razor sayfalar, Razor derleme kaynakları, etiket yardımcıları ve daha fazlasını içeren bir DERLEMEYI (dll) paylaşabilirsiniz. Birden çok projedeki kodu çoğaltmak için bir derlemeyi paylaşma tercih edilir.

ASP.NET Core uygulamalar, içindeki <xref:System.Web.WebPages.ApplicationPart>özellikleri yükler. Sınıfı <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> , bir derleme tarafından desteklenen bir uygulama parçasını temsil eder.

## <a name="load-aspnet-core-features"></a>ASP.NET Core özellikleri yükle

ASP.NET Core özelliklerini `ApplicationPart` ( `AssemblyPart` denetleyiciler, görünüm bileşenleri vs.) bulup yüklemek için ve sınıflarını kullanın. Uygulama <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> parçalarını ve özellik sağlayıcılarını izler. `ApplicationPartManager`Şu şekilde yapılandırılır `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Aşağıdaki kod, kullanarak `ApplicationPartManager` `AssemblyPart`yapılandırma için alternatif bir yaklaşım sağlar:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Önceki iki kod örneği bir derlemeden yüklenir `SharedController` . `SharedController` Uygulamanın projesinde değil. Bkz. [Webappparts çözüm](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) örneği indirmesi.

### <a name="include-views"></a>Görünümleri dahil et

Derleme içindeki görünümleri dahil etmek için bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class) kullanın.

### <a name="prevent-loading-resources"></a>Kaynakları yüklemeyi engelle

Uygulama bölümleri, belirli bir derleme veya konumdaki kaynakları yüklemeyi *önlemek* için kullanılabilir. Kullanılabilir kaynakları gizlemek veya saklamak için <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> koleksiyonun üyelerini ekleyin veya kaldırın. `ApplicationParts` Koleksiyondaki girişlerin sırası önemli değildir. Kapsayıcısını, `ApplicationPartManager` kapsayıcıdaki hizmetleri yapılandırmak için kullanmadan önce yapılandırın. Örneğin, öğesini çağırmadan `ApplicationPartManager` `AddControllersAsServices`önce öğesini yapılandırın. Kaynağı `Remove` kaldırmak için `ApplicationParts` koleksiyonda çağrı yapın.

Aşağıdaki kod uygulamadan kaldırmak <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> `MyDependentLibrary` için kullanır:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

, `ApplicationPartManager` Şunlar için parçalar içerir:

* Uygulamanın derlemesi ve bağımlı derlemeleri.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Uygulama özelliği sağlayıcıları

Uygulama özelliği sağlayıcıları uygulama parçalarını inceler ve bu parçalar için özellikler sağlar. Aşağıdaki ASP.NET Core özellikleri için yerleşik özellik sağlayıcıları vardır:

* [Denetleyiciler](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Etiket Yardımcıları](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Bileşenleri görüntüle](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Özellik sağlayıcıları öğesinden <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>devralınır, burada `T` özelliğin türüdür. Özellik sağlayıcıları, daha önce listelenen özellik türlerinden herhangi biri için uygulanabilir. İçindeki `ApplicationPartManager.FeatureProviders` özellik sağlayıcılarının sırası çalışma süresi davranışından etkileyebilir. Daha sonra eklenen sağlayıcılar, daha önce eklenen sağlayıcıların yaptığı eylemlere yanıt verebilir.

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

* Ayarın `applicationName` , bulma için kullanılan kök derlemeye ayarlanması gerekir. Bulma için kullanılan kök derleme normalde giriş noktası derlemesidir.
* Kök derlemenin, bulma için kullanılan bölümlere bir başvuruya sahip olması gerekir. Başvuru doğrudan veya geçişli olabilir.
* Kök derlemenin Web SDK 'sına başvurması gerekiyor.
  * ASP.NET Core Framework 'te, bulma için kullanılan kök derlemeye öznitelikleri damgalayan özel yapı mantığı vardır.

::: moniker-end
