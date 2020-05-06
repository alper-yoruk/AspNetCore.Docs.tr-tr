---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: ASP.NET Core sayfalarındaki Razor SAYFALARıN, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776740"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK 'Sı

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Genel Bakış

, [!INCLUDE[](~/includes/2.1-SDK.md)] `Microsoft.NET.Sdk.Razor` MSBuild SDK 'sını (Razor SDK) içerir. Razor SDK 'Sı:

::: moniker range=">= aspnetcore-3.0"

* ASP.NET Core MVC tabanlı veya [Blazor](xref:blazor/index) projeleri için [Razor](xref:mvc/views/razor) dosyaları içeren projeleri derlemek, paketlemek ve yayımlamak için gereklidir.
* , Razor (*. cshtml* veya *. Razor*) dosyalarının derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir.

Razor `Content` SDK, `Include` `**\*.cshtml` ve `**\*.razor` glob desenlerine ayarlanmış öznitelikleri olan öğeleri içerir. Eşleşen dosyalar yayımlandı.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* ASP.NET Core MVC tabanlı projeler için [Razor](xref:mvc/views/razor) dosyaları içeren projeleri oluşturma, paketleme ve yayımlama ile ilgili deneyimi standartlaştırır.
* Razor dosyalarının derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir.

Razor SDK, `Content` `Include` `**\*.cshtml` glob düzenine ayarlanmış bir özniteliği olan bir öğe içerir. Eşleşen dosyalar yayımlandı.

::: moniker-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Razor SDK 'sını kullanma

Çoğu Web uygulaması Razor SDK 'ya açıkça başvurmak için gerekli değildir.

::: moniker range=">= aspnetcore-3.0"

Razor görünümlerini veya Razor Pages içeren sınıf kitaplıkları oluşturmak için Razor SDK 'yı kullanmak için Razor sınıf kitaplığı (RCL) proje şablonuyla başlamasını öneririz. Blazor (*. Razor*) dosyalarını derlemek için kullanılan bir RCL, [Microsoft. Aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) paketine en az bir başvuru gerektirir. Razor görünümlerini veya sayfalarını (*. cshtml* dosyaları) derlemek için kullanılan bir RCL, hedeflemesi `netcoreapp3.0` veya daha yenisini gerektirir ve proje dosyasında `FrameworkReference` [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) öğesine sahip olmalıdır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor görünümlerini veya Razor Pages içeren sınıf kitaplıkları derlemek için Razor SDK 'yı kullanmak için:

* Yerine `Microsoft.NET.Sdk.Razor` şunu kullanın `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Genellikle, Razor Pages ve Razor görünümlerini `Microsoft.AspNetCore.Mvc` derlemek ve derlemek için gereken ek bağımlılıklar almak için bir paket başvurusu gerekir. En azından, projenizin paket başvurularını şu şekilde eklemesi gerekir:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` Paket, proje için Razor derleme görevleri ve hedefleri sağlar.

  Önceki paketler ' de `Microsoft.AspNetCore.Mvc`yer alır. Aşağıdaki biçimlendirme, bir ASP.NET Core Razor Pages uygulaması için Razor dosyaları derlemek için Razor SDK 'sını kullanan bir proje dosyası gösterir:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Ve `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` paketleri, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içine dahil edilmiştir. Ancak, sürüm-daha az `Microsoft.AspNetCore.App` paket başvurusu, en son sürümünü içermeyen uygulamaya bir metapackage sağlar `Microsoft.AspNetCore.Razor.Design`. Projeler, Razor için en son derleme `Microsoft.AspNetCore.Razor.Design` zamanı düzeltmelerinin `Microsoft.AspNetCore.Mvc`dahil olması için tutarlı bir sürüme (veya) başvurmalıdır. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/aspnet/Razor/issues/2553)bakın.

::: moniker-end

### <a name="properties"></a>Özellikler

Aşağıdaki özellikler, bir proje derlemesinin parçası olarak Razor SDK davranışını denetler:

* `RazorCompileOnBuild`&ndash; Ne `true`zaman, proje oluşturmanın bir parçası olarak Razor derlemesini derler ve yayar. Varsayılan olarak `true`olur.
* `RazorCompileOnPublish`&ndash; Ne `true`zaman, projenin yayımlaması kapsamında Razor derlemesini derler ve yayar. Varsayılan olarak `true`olur.

Aşağıdaki tablodaki Özellikler ve öğeler, Razor SDK 'ya giriş ve çıkış yapılandırmak için kullanılır.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ASP.NET Core 3,0 ' den başlayarak, MVC görünümleri veya Razor Pages proje dosyasındaki ya `RazorCompileOnBuild` `RazorCompileOnPublish` da MSBuild özellikleri devre dışı bırakılmışsa varsayılan olarak sunulmuyor. Uygulama, *. cshtml* dosyalarını işlemek üzere çalışma zamanı derlemesini kullanıyorsa, uygulamalar [Microsoft. Aspnetcore. Mvc. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) paketine açık bir başvuru eklememelidir.

::: moniker-end

| Öğeler | Açıklama |
| ----- | ----------- |
| `RazorGenerate` | Kod oluşturmaya giriş olan öğe öğeleri (*. cshtml* dosyaları). |
| `RazorComponent` | Razor bileşen kodu oluşturmaya giriş olan öğe öğeleri (*. Razor* dosyaları). |
| `RazorCompile` | Razor derleme hedeflerine giriş olan öğe öğeleri (*. cs* dosyaları). Razor derlemesinde `ItemGroup` derlenecek ek dosyaları belirtmek için bunu kullanın. |
| `RazorTargetAssemblyAttribute` | Razor derlemesi için kod oluşturma öznitelikleri için kullanılan öğe öğeleri. Örneğin:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Oluşturulan Razor derlemesine gömülü kaynaklar olarak eklenen öğe öğeleri. |

::: moniker range=">= aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Razor tarafından üretilen derlemenin dosya adı (uzantısı olmadan). |
| `RazorOutputPath` | Razor çıkış dizini. |
| `RazorCompileToolset` | Razor derlemesini derlemek için kullanılan araç takımını tespit etmek için kullanılır. Geçerli değerler `Implicit`, `RazorSDK`, ve. `PrecompilationTool` |
| [Enabledefaultcontentıtems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne `true`zaman, *Web. config*, *. JSON*ve *. cshtml* dosyalarını projeye içerik olarak ekler. Aracılığıyla `Microsoft.NET.Sdk.Web`başvurulduğunda, *Wwwroot* ve yapılandırma dosyaları altındaki dosyalar da dahil edilmiştir. |
| `EnableDefaultRazorGenerateItems` | Ne `true`zaman `Content` , `RazorGenerate` öğelerdeki öğelerden *. cshtml* dosyalarını içerir. |
| `GenerateRazorTargetAssemblyInfo` | Ne `true`zaman, tarafından `RazorAssemblyAttribute` belirtilen öznitelikleri içeren bir *. cs* dosyası oluşturur ve derleme çıkışında dosyasını içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne `true`zaman, ' a bir varsayılan derleme öznitelikleri kümesi `RazorAssemblyAttribute`ekler. |
| `CopyRazorGenerateFilesToPublishDirectory` | Ne `true`zaman, `RazorGenerate` öğe (*. cshtml*) dosyalarını yayımlama dizinine kopyalar. Genellikle, derleme zamanında veya yayımlama zamanında derlemeye katılırsanız yayımlanmış bir uygulama için Razor dosyaları gerekli değildir. Varsayılan olarak `false`olur. |
| `PreserveCompilationReferences` | Ne `true`zaman, başvuru derleme öğelerini yayımlama dizinine kopyalayın. Genellikle, derleme zamanında veya yayımlama zamanında Razor derlemesi gerçekleşirse, yayımlanan bir uygulama için başvuru derlemeleri gerekli değildir. Yayımlanmış uygulamanız `true` çalışma zamanı derlemesi gerektiriyorsa, olarak ayarlayın. Örneğin, uygulama çalışma zamanında `true` *. cshtml* dosyalarını değiştirirse veya gömülü görünümleri kullanıyorsa, değerini olarak ayarlayın. Varsayılan olarak `false`olur. |
| `IncludeRazorContentInPack` | Ne `true`zaman, tüm Razor içerik öğeleri (*. cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlenir. Varsayılan olarak `false`olur. |
| `EmbedRazorGenerateSources` | Ne `true`zaman, RazorGenerate (*. cshtml*) öğelerini oluşturulan Razor derlemesine gömülü dosyalar olarak ekler. Varsayılan olarak `false`olur. |
| `UseRazorBuildServer` | Ne `true`zaman, kod oluşturma işinin yükünü boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan değer olarak değeridir `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Ne `true`zaman, SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikler üretir. |
| `DefaultWebContentItemExcludes` | Web veya Razor SDK 'Yı hedefleyen projelerde `Content` öğe grubundan çıkarılacak öğe öğeleri için glob bir model |
| `ExcludeConfigFilesFromBuildOutput` | Ne `true`zaman, *. config* ve *. JSON* dosyaları derleme çıkış dizinine kopyalanmaz. |
| `AddRazorSupportForMvc` | Ne `true`zaman, MVC görünümlerini veya Razor Pages içeren uygulamalar oluştururken gereklı olan MVC yapılandırmasına yönelik destek eklemek için Razor SDK 'sını yapılandırır. Bu özellik, Web SDK 'sını hedefleyen .NET Core 3,0 veya üzeri projeler için örtük olarak ayarlanmıştır |
| `RazorLangVersion` | Hedeflenecek Razor dilinin sürümü. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Tarafından Razorüretilen derlemenin dosya adı (uzantısı olmadan). |
| `RazorOutputPath` | Razor Çıkış dizini. |
| `RazorCompileToolset` | Razor Derlemeyi derlemek için kullanılan araç takımını tespit etmek için kullanılır. Geçerli değerler `Implicit`, `RazorSDK`, ve. `PrecompilationTool` |
| [Enabledefaultcontentıtems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne `true`zaman, *Web. config*, *. JSON*ve *. cshtml* dosyalarını projeye içerik olarak ekler. Aracılığıyla `Microsoft.NET.Sdk.Web`başvurulduğunda, *Wwwroot* ve yapılandırma dosyaları altındaki dosyalar da dahil edilmiştir. |
| `EnableDefaultRazorGenerateItems` | Ne `true`zaman `Content` , `RazorGenerate` öğelerdeki öğelerden *. cshtml* dosyalarını içerir. |
| `GenerateRazorTargetAssemblyInfo` | Ne `true`zaman, tarafından `RazorAssemblyAttribute` belirtilen öznitelikleri içeren bir *. cs* dosyası oluşturur ve derleme çıkışında dosyasını içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne `true`zaman, ' a bir varsayılan derleme öznitelikleri kümesi `RazorAssemblyAttribute`ekler. |
| `CopyRazorGenerateFilesToPublishDirectory` | Ne `true`zaman, `RazorGenerate` öğe (*. cshtml*) dosyalarını yayımlama dizinine kopyalar. Genellikle, Razor derleme zamanında veya yayımlama zamanında derlemeye katılırsanız yayımlanmış bir uygulama için dosyalar gerekli değildir. Varsayılan olarak `false`olur. |
| `CopyRefAssembliesToPublishDirectory` | Ne `true`zaman, başvuru derleme öğelerini yayımlama dizinine kopyalayın. Genellikle, derleme zamanında veya yayımlama zamanında Razor derleme gerçekleşirse yayımlanmış bir uygulama için başvuru derlemeleri gerekli değildir. Yayımlanmış uygulamanız `true` çalışma zamanı derlemesi gerektiriyorsa, olarak ayarlayın. Örneğin, uygulama çalışma zamanında `true` *. cshtml* dosyalarını değiştirirse veya gömülü görünümleri kullanıyorsa, değerini olarak ayarlayın. Varsayılan olarak `false`olur. |
| `IncludeRazorContentInPack` | Ne `true`zaman, Razor tüm içerik öğeleri (*. cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlenir. Varsayılan olarak `false`olur. |
| `EmbedRazorGenerateSources` | Ne `true`zaman, RazorGenerate (*. cshtml*) öğelerini oluşturulan Razor derlemeye gömülü dosyalar olarak ekler. Varsayılan olarak `false`olur. |
| `UseRazorBuildServer` | Ne `true`zaman, kod oluşturma işinin yükünü boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan değer olarak değeridir `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Ne `true`zaman, SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma zamanında MVC tarafından kullanılan ek öznitelikler üretir. |
| `DefaultWebContentItemExcludes` | Web veya `Content` Razor SDK 'yı hedefleyen projelerde öğe grubundan çıkarılacak öğe öğeleri için glob bir model |
| `ExcludeConfigFilesFromBuildOutput` | Ne `true`zaman, *. config* ve *. JSON* dosyaları derleme çıkış dizinine kopyalanmaz. |
| `AddRazorSupportForMvc` | Ne `true`zaman, MVC Razor görünümlerini veya Razor SAYFALARıNı içeren uygulamalar oluştururken gerekli olan MVC yapılandırmasına yönelik destek eklemek için SDK 'yı yapılandırır. Bu özellik, Web SDK 'sını hedefleyen .NET Core 3,0 veya üzeri projeler için örtük olarak ayarlanmıştır |
| `RazorLangVersion` | Hedeflenecek Razor dilin sürümü. |

::: moniker-end

Özellikler hakkında daha fazla bilgi için bkz. [MSBuild özellikleri](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Hedefler

Razor SDK iki birincil hedefi tanımlar:

* `RazorGenerate`&ndash; Kod, `RazorGenerate` öğe öğelerinden *. cs* dosyaları oluşturur. Bu hedeften `RazorGenerateDependsOn` önce veya sonra çalışabilecek ek hedefleri belirtmek için özelliğini kullanın.
* `RazorCompile`&ndash; İçinde oluşturulan *. cs* dosyalarını bir Razor derlemede derler. Bu hedeften `RazorCompileDependsOn` önce veya sonra çalışabilecek ek hedefleri belirtmek için öğesini kullanın.
* `RazorComponentGenerate`&ndash; Kod, öğe öğeleri için `RazorComponent` *. cs* dosyaları oluşturur. Bu hedeften `RazorComponentGenerateDependsOn` önce veya sonra çalışabilecek ek hedefleri belirtmek için özelliğini kullanın.

### <a name="runtime-compilation-of-razor-views"></a>Razor Görünümlerin çalışma zamanı derlemesi

* Varsayılan olarak Razor SDK, çalışma zamanı derlemesini gerçekleştirmek için gerekli olan başvuru derlemelerini yayımlamaz. Bu durum, uygulama modeli çalışma zamanı derlemesini&mdash;kullandığında derleme hatalarıyla sonuçlanır. uygulama yayımlandıktan sonra uygulama gömülü görünümleri veya değişiklik görünümlerini kullanır. Başvuru `CopyRefAssembliesToPublishDirectory` derlemelerini `true` yayımlamaya devam etmek için olarak ayarlayın.

* Bir Web uygulaması için uygulamanızın `Microsoft.NET.Sdk.Web` SDK 'yı hedeflediğinden emin olun.

## <a name="razor-language-version"></a>Razordil sürümü

`Microsoft.NET.Sdk.Web` SDK 'yı hedeflerken, Razor dil sürümü uygulamanın hedef Framework sürümünden algılanır. `Microsoft.NET.Sdk.Razor` SDK 'yı hedefleyen projeler veya uygulamanın çıkarılan değerden farklı Razor bir dil sürümü gerektirmesi durumunda, uygulamanın proje dosyasındaki `<RazorLangVersion>` özelliği ayarlanarak bir sürüm yapılandırılabilir:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razordil sürümü, için oluşturulduğu çalışma zamanının sürümü ile sıkı bir şekilde tümleşiktir. Çalışma zamanı için tasarlanmamış bir dil sürümünü hedeflemek desteklenmez ve muhtemelen derleme hataları üretir.

## <a name="additional-resources"></a>Ek kaynaklar

* [.NET Core için csproj biçimine eklemeler](/dotnet/core/tools/csproj)
* [Ortak MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items)
