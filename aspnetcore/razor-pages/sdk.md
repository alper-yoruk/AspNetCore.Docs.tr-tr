---
title: ASP.NET Çekirdek Jilet SDK
author: Rick-Anderson
description: ASP.NET Core'daki Razor Pages'in sayfa odaklı senaryoları kodlamayı MVC kullanmaktan nasıl daha kolay ve üretken hale getirir hale ettiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321006"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Çekirdek Jilet SDK

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Genel Bakış

`Microsoft.NET.Sdk.Razor` MsBuild [!INCLUDE[](~/includes/2.1-SDK.md)] SDK (Razor SDK) içerir. Jilet SDK:

::: moniker range=">= aspnetcore-3.0"

* Core MVC tabanlı veya [Blazor](xref:blazor/index) projeleri için [ASP.NET Razor](xref:mvc/views/razor) dosyaları içeren projeler oluşturmak, paketlemek ve yayınlamak için gereklidir.
* Razor (*.cshtml* veya *.razor*) dosyalarının derlemesini özelleştirmeye olanak tanıyan önceden tanımlanmış hedefler, özellikler ve öğeler kümesi ni içerir.

Razor SDK, `Content` `Include` desenlere göre `**\*.cshtml` ayarlanmış `**\*.razor` özelliklere sahip öğeler içerir. Eşleşen dosyalar yayımlanır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Core MVC tabanlı ASP.NET projeler için [Jilet](xref:mvc/views/razor) dosyaları içeren geliştirme, paketleme ve yayımlama projeleri ile ilgili deneyimi standartlaştırır.
* Razor dosyalarının derlemisini özelleştirmeye izin veren önceden tanımlanmış hedefler, özellikler ve öğeler kümesi ni içerir.

Razor SDK globbing desen `Include` ayarlanmış `**\*.cshtml` bir öznitelik ile bir `Content` öğe içerir. Eşleşen dosyalar yayımlanır.

::: moniker-end

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Razor SDK kullanın

Çoğu web uygulamasının Razor SDK'ya açıkça başvurması gerekmez.

::: moniker range=">= aspnetcore-3.0"

Razor görünümleri veya Razor Sayfaları içeren sınıf kitaplıkları oluşturmak için Razor SDK'yı kullanmak için, Razor sınıf kitaplığı (RCL) proje şablonundan başlamanızı öneririz. Blazor (*.razor*) dosyalarını oluşturmak için kullanılan bir RCL, [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) paketine en az bir başvuru gerektirir. Razor görünümleri veya sayfaları *(.cshtml* dosyaları) oluşturmak için kullanılan bir `netcoreapp3.0` RCL en `FrameworkReference` az hedefleme gerektirir veya daha sonra ve proje dosyasında [Microsoft.AspNetCore.App metapaketi](xref:fundamentals/metapackage-app) bir vardır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor SDK'yı kullanarak Razor görünümleri veya Jilet Sayfaları içeren sınıf kitaplıkları oluşturmak için:

* Bunun `Microsoft.NET.Sdk.Razor` yerine `Microsoft.NET.Sdk`kullanın:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Genellikle, Razor Sayfaları `Microsoft.AspNetCore.Mvc` ve Razor görünümleri oluşturmak ve derlemek için gereken ek bağımlılıkları almak için bir paket başvurusu gereklidir. En azından, projeniz aşağıdakilere paket başvuruları eklemelidir:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Paket, `Microsoft.AspNetCore.Razor.Design` proje için Razor derleme görevlerini ve hedeflerini sağlar.

  Önceki paketler `Microsoft.AspNetCore.Mvc`. Aşağıdaki biçimlendirme, ASP.NET Core Razor Pages uygulaması için Razor dosyaları oluşturmak için Razor SDK kullanan bir proje dosyasını gösterir:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Ve `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` paketleri [Microsoft.AspNetCore.App metapaketi](xref:fundamentals/metapackage-app)dahildir. Ancak, sürüm-az `Microsoft.AspNetCore.App` paket başvurusu, uygulamaiçin en son sürümü içermeyen bir `Microsoft.AspNetCore.Razor.Design`meta paket sağlar. Projeler, Razor için `Microsoft.AspNetCore.Razor.Design` en `Microsoft.AspNetCore.Mvc`son yapı zamanı düzeltmelerinin dahil edilebilmeleri için tutarlı bir sürümüne (veya) başvurmalıdır. Daha fazla bilgi için [bu GitHub sorununa](https://github.com/aspnet/Razor/issues/2553)bakın.

::: moniker-end

### <a name="properties"></a>Özellikler

Aşağıdaki özellikler, bir proje oluşturmanın bir parçası olarak Razor'ın SDK davranışını denetler:

* `RazorCompileOnBuild`&ndash; Ne `true`zaman , derler ve projenin oluşturulmasının bir parçası olarak Razor montaj yayır. Varsayılan `true`değer.
* `RazorCompileOnPublish`&ndash; Ne `true`zaman , derler ve projenin yayınlanmasının bir parçası olarak Razor montaj yayır. Varsayılan `true`değer.

Aşağıdaki tablodaki özellikler ve öğeler, giriş ve çıktıyı Razor SDK'ya yapılandırmak için kullanılır.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Core 3.0 ASP.NET başlayarak, proje dosyasındaki `RazorCompileOnBuild` veya `RazorCompileOnPublish` MSBuild özellikleri devre dışı bırakılırsa, MVC Görünümler veya Jilet Sayfaları varsayılan olarak sunulmaz. Uygulama *.cshtml* dosyalarını işlemek için çalışma süresi derlemesine dayanıyorsa, uygulamalar [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) paketine açık bir başvuru eklemelidir.

::: moniker-end

| Öğeler | Açıklama |
| ----- | ----------- |
| `RazorGenerate` | Kod oluşturma girişleri olan öğe öğeleri (*.cshtml* dosyaları). |
| `RazorComponent` | Razor bileşen kodu oluşturma girişi olan öğe öğeleri *(.jilet* dosyaları). |
| `RazorCompile` | Razor derleme hedeflerine giriş yapan öğe öğeleri (*.cs* dosyaları). Razor `ItemGroup` derlemesine derlenecek ek dosyaları belirtmek için bunu kullanın. |
| `RazorTargetAssemblyAttribute` | Razor derlemesi için öznitelikleri kodlamak için kullanılan öğe öğeleri. Örneğin:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Oluşturulan Razor derlemesine katışılmış kaynak olarak eklenen öğe öğeleri. |

::: moniker range=">= aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Razor tarafından üretilen derlemenin dosya adı (uzantısı olmadan). |
| `RazorOutputPath` | Razor çıkış dizini. |
| `RazorCompileToolset` | Razor montajını oluşturmak için kullanılan araç kümesini belirlemek için kullanılır. Geçerli değerler `Implicit` `RazorSDK`, `PrecompilationTool`ve . |
| [Varsayılan İçerik Öğeleri etkinleştirme](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne `true`zaman , *web.config*, *.json*, ve *.cshtml* dosyaları projede içerik olarak içerir. Wwwroot ve `Microsoft.NET.Sdk.Web`config *wwwroot* dosyaları altında dosyaları da dahil edilir üzerinden başvurulan. |
| `EnableDefaultRazorGenerateItems` | Ne `true`zaman , öğelerdeki `RazorGenerate` `Content` öğelerden *.cshtml* dosyaları içerir. |
| `GenerateRazorTargetAssemblyInfo` | Ne `true`zaman , tarafından `RazorAssemblyAttribute` belirtilen öznitelikleri içeren bir *.cs* dosyası oluşturur ve derleme çıktısında dosya içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne `true`zaman , `RazorAssemblyAttribute`varsayılan bir derleme öznitelikleri kümesi ekler . |
| `CopyRazorGenerateFilesToPublishDirectory` | Öğeleri (*.cshtml*) dosyalarını yayımlama dizinine kopyaladığında. `true` `RazorGenerate` Genellikle, razor dosyaları, derlemeye yapı zamanında veya yayımlama zamanında katılıyorsa, yayımlanmış bir uygulama için gerekli değildir. Varsayılan `false`değer. |
| `PreserveCompilationReferences` | Başvuru `true`derleme öğelerini yayımdizinine kopyaladığında. Genellikle, Razor derlemesi yapı zamanında veya yayımlama zamanında oluşuyorsa, yayınlanmış bir uygulama için başvuru derlemeleri gerekmez. Yayınlanmış `true` uygulamanızın çalışma zamanı derlemesi gerektirip gerektirmeyecek şekilde ayarlayın. Örneğin, uygulamanın `true` *.cshtml* dosyalarını çalışma zamanında modifiye edip etmeyecek veya katışılmış görünümler kullanıyorsa değerini ayarlayın. Varsayılan `false`değer. |
| `IncludeRazorContentInPack` | Tüm `true`Razor içerik öğeleri *(.cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlendiğinde. Varsayılan `false`değer. |
| `EmbedRazorGenerateSources` | `true`Oluşturulan Razor derlemesine gömülü dosyalar olarak RazorGenerate (*.cshtml*) öğeleri ni ekler. Varsayılan `false`değer. |
| `UseRazorBuildServer` | Ne `true`zaman, kod oluşturma işi boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan `UseSharedCompilation`değer. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true`SDK, uygulama parçası bulma gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikleri oluşturur. |
| `DefaultWebContentItemExcludes` | Web veya Razor SDK'yı hedefleyen projelerde `Content` madde grubundan dışlanacak öğe öğeleri için parlama deseni |
| `ExcludeConfigFilesFromBuildOutput` | Ne `true`zaman , *.config* ve *.json* dosyaları yapı çıktı dizini kopyalanmaz. |
| `AddRazorSupportForMvc` | `true`MVC görünümleri veya Razor Sayfaları içeren uygulamalar yaparken gereken MVC yapılandırması için destek eklemek için Razor SDK'yı yapılandırDığında. Bu özellik, Web SDK'yı hedefleyen .NET Core 3.0 veya daha sonraki projeler için örtülü olarak ayarlanır |
| `RazorLangVersion` | Hedef için Razor Dil sürümü. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Razor tarafından üretilen derlemenin dosya adı (uzantısı olmadan). |
| `RazorOutputPath` | Razor çıkış dizini. |
| `RazorCompileToolset` | Razor montajını oluşturmak için kullanılan araç kümesini belirlemek için kullanılır. Geçerli değerler `Implicit` `RazorSDK`, `PrecompilationTool`ve . |
| [Varsayılan İçerik Öğeleri etkinleştirme](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne `true`zaman , *web.config*, *.json*, ve *.cshtml* dosyaları projede içerik olarak içerir. Wwwroot ve `Microsoft.NET.Sdk.Web`config *wwwroot* dosyaları altında dosyaları da dahil edilir üzerinden başvurulan. |
| `EnableDefaultRazorGenerateItems` | Ne `true`zaman , öğelerdeki `RazorGenerate` `Content` öğelerden *.cshtml* dosyaları içerir. |
| `GenerateRazorTargetAssemblyInfo` | Ne `true`zaman , tarafından `RazorAssemblyAttribute` belirtilen öznitelikleri içeren bir *.cs* dosyası oluşturur ve derleme çıktısında dosya içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne `true`zaman , `RazorAssemblyAttribute`varsayılan bir derleme öznitelikleri kümesi ekler . |
| `CopyRazorGenerateFilesToPublishDirectory` | Öğeleri (*.cshtml*) dosyalarını yayımlama dizinine kopyaladığında. `true` `RazorGenerate` Genellikle, razor dosyaları, derlemeye yapı zamanında veya yayımlama zamanında katılıyorsa, yayımlanmış bir uygulama için gerekli değildir. Varsayılan `false`değer. |
| `CopyRefAssembliesToPublishDirectory` | Başvuru `true`derleme öğelerini yayımdizinine kopyaladığında. Genellikle, Razor derlemesi yapı zamanında veya yayımlama zamanında oluşuyorsa, yayınlanmış bir uygulama için başvuru derlemeleri gerekmez. Yayınlanmış `true` uygulamanızın çalışma zamanı derlemesi gerektirip gerektirmeyecek şekilde ayarlayın. Örneğin, uygulamanın `true` *.cshtml* dosyalarını çalışma zamanında modifiye edip etmeyecek veya katışılmış görünümler kullanıyorsa değerini ayarlayın. Varsayılan `false`değer. |
| `IncludeRazorContentInPack` | Tüm `true`Razor içerik öğeleri *(.cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlendiğinde. Varsayılan `false`değer. |
| `EmbedRazorGenerateSources` | `true`Oluşturulan Razor derlemesine gömülü dosyalar olarak RazorGenerate (*.cshtml*) öğeleri ni ekler. Varsayılan `false`değer. |
| `UseRazorBuildServer` | Ne `true`zaman, kod oluşturma işi boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan `UseSharedCompilation`değer. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true`SDK, uygulama parçası bulma gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikleri oluşturur. |
| `DefaultWebContentItemExcludes` | Web veya Razor SDK'yı hedefleyen projelerde `Content` madde grubundan dışlanacak öğe öğeleri için parlama deseni |
| `ExcludeConfigFilesFromBuildOutput` | Ne `true`zaman , *.config* ve *.json* dosyaları yapı çıktı dizini kopyalanmaz. |
| `AddRazorSupportForMvc` | `true`MVC görünümleri veya Razor Sayfaları içeren uygulamalar yaparken gereken MVC yapılandırması için destek eklemek için Razor SDK'yı yapılandırDığında. Bu özellik, Web SDK'yı hedefleyen .NET Core 3.0 veya daha sonraki projeler için örtülü olarak ayarlanır |
| `RazorLangVersion` | Hedef için Razor Dil sürümü. |

::: moniker-end

Özellikler hakkında daha fazla bilgi için [MSBuild özelliklerine](/visualstudio/msbuild/msbuild-properties)bakın.

### <a name="targets"></a>Hedefler

Razor SDK iki ana hedefi tanımlar:

* `RazorGenerate`&ndash; Kod öğe öğelerinden `RazorGenerate` *.cs* dosyaları oluşturur. Bu `RazorGenerateDependsOn` hedeften önce veya sonra çalıştırılabilen ek hedefler belirtmek için özelliği kullanın.
* `RazorCompile`&ndash; Oluşturulan *.cs* dosyalarını Bir Razor derlemesinde derler. Bu `RazorCompileDependsOn` hedeften önce veya sonra çalıştırılabilen ek hedefler belirtmek için kullanın.
* `RazorComponentGenerate`&ndash; Kod, öğe öğeleri için `RazorComponent` *.cs* dosyaları oluşturur. Bu `RazorComponentGenerateDependsOn` hedeften önce veya sonra çalıştırılabilen ek hedefler belirtmek için özelliği kullanın.

### <a name="runtime-compilation-of-razor-views"></a>Razor görünümlerinin çalışma zamanı derlemesi

* Varsayılan olarak, Razor SDK çalışma zamanı derlemesi gerçekleştirmek için gerekli olan başvuru derlemeleri yayımlamaz. Bu, uygulama modeli örneğin çalışma zamanı derlemesine&mdash;dayandığında, uygulama katıştırılmış görünümleri kullandığında veya uygulama yayınlandıktan sonra görünümleri değiştirdiğinde derleme hatalarına neden olabilir. Başvuru `CopyRefAssembliesToPublishDirectory` `true` derlemelerini yayımlamaya devam edecek şekilde ayarlayın.

* Bir web uygulaması için uygulamanızın SDK'yı `Microsoft.NET.Sdk.Web` hedeflediğinden emin olun.

## <a name="razor-language-version"></a>Razor dil sürümü

`Microsoft.NET.Sdk.Web` SDK hedeflenirken, Razor dil sürümü uygulamanın hedef çerçeve sürümünden çıkarılır. SDK'yı `Microsoft.NET.Sdk.Razor` hedefleyen projeler de veya uygulamanın çıkarılan değerden farklı bir Razor dil sürümü gerektirdiği nadir durumlarda, `<RazorLangVersion>` uygulamanın proje dosyasındaki özelliği ayarlayarak bir sürüm yapılandırılabilir:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor'ın dil sürümü, için inşa edildiği çalışma zamanının sürümüyle sıkıca entegre edilmiştir. Çalışma zamanı için tasarlanmamış bir dil sürümünü hedeflemek desteklenmez ve büyük olasılıkla yapı hataları oluşturur.

## <a name="additional-resources"></a>Ek kaynaklar

* [.NET Core için csproj formatına eklemeler](/dotnet/core/tools/csproj)
* [Ortak MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items)
