---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: RazorASP.NET Core sayfalarındaki sayfaların, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: b960460a50558a11bc47f9a1844931aa32e3d696
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021425"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Genel Bakış

, [!INCLUDE[](~/includes/2.1-SDK.md)] `Microsoft.NET.Sdk.Razor` MSBuild SDK 'sını ( Razor SDK) içerir. RazorSDK:

::: moniker range=">= aspnetcore-3.0"

* [Razor](xref:mvc/views/razor)ASP.NET Core MVC tabanlı veya projeleri için dosya içeren projeleri derlemek, paketlemek ve yayımlamak için gereklidir [Blazor](xref:blazor/index) .
* Razor(*. Cshtml* veya *. Razor*) dosyalarının derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir.

RazorSDK, `Content` `Include` `**\*.cshtml` ve `**\*.razor` Glob desenlerine ayarlanmış öznitelikleri olan öğeleri içerir. Eşleşen dosyalar yayımlandı.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Razor](xref:mvc/views/razor)ASP.NET Core MVC tabanlı projeler için dosya içeren projeleri oluşturma, paketleme ve yayımlama ile ilgili deneyimi standartlaştırır.
* , Dosya derlemesini özelleştirmeye izin veren bir dizi önceden tanımlanmış hedef, özellik ve öğe içerir Razor .

RazorSDK, `Content` `Include` Glob düzenine ayarlanmış bir özniteliği olan bir öğe içerir `**\*.cshtml` . Eşleşen dosyalar yayımlandı.

::: moniker-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>RazorSDK 'yı kullanma

Çoğu Web uygulaması SDK 'ya açık olarak başvurmak için gerekli değildir Razor .

::: moniker range=">= aspnetcore-3.0"

RazorSDK 'yı, görünümler veya sayfalar içeren sınıf kitaplıkları oluşturmak üzere kullanmak için Razor Razor , Razor sınıf kitaplığı (RCL) proje şablonuyla başlamasını öneririz. Blazor(*. Razor*) dosyalarını derlemek için kullanılan bir RCL, [Microsoft. aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) paketine en az bir başvuru gerektirir. RazorGörünümleri veya sayfaları (*. cshtml* dosyaları) oluşturmak için kullanılan bir RCL `netcoreapp3.0` , hedeflemesi veya daha yenisini gerektirir ve `FrameworkReference` Proje dosyasında [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) öğesine sahip olmalıdır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RazorGörünümleri veya sayfaları içeren sınıf kitaplıkları oluşturmak için SDK 'yı kullanmak için Razor Razor :

* `Microsoft.NET.Sdk.Razor`Yerine şunu kullanın `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Genellikle, `Microsoft.AspNetCore.Mvc` sayfaları ve görünümleri derlemek ve derlemek için gereken ek bağımlılıklar almak için bir paket başvurusu gerekir Razor Razor . En azından, projenizin paket başvurularını şu şekilde eklemesi gerekir:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design`Paket, Razor proje için derleme görevlerini ve hedeflerini sağlar.

  Önceki paketler ' de yer alır `Microsoft.AspNetCore.Mvc` . Aşağıdaki biçimlendirme, Razor Razor bir ASP.NET Core Pages uygulamasının dosyalarını derlemek için SDK 'yı kullanan bir proje dosyası gösterir Razor :

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`Ve `Microsoft.AspNetCore.Mvc.Razor.Extensions` paketleri, [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içine dahil edilmiştir. Ancak, sürüm-daha az `Microsoft.AspNetCore.App` paket başvurusu, en son sürümünü içermeyen uygulamaya bir metapackage sağlar `Microsoft.AspNetCore.Razor.Design` . Projeleri `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc` , için en son derleme zamanı düzeltmelerinin dahil olması için tutarlı bir sürüme (veya) başvurmalıdır Razor . Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/aspnet/Razor/issues/2553)bakın.

::: moniker-end

### <a name="properties"></a>Özellikler

Aşağıdaki özellikler, Razor proje derlemesinin bir parçası olarak SDK davranışını denetler:

* `RazorCompileOnBuild`: Ne zaman `true` , Razor derlemeyi proje oluşturmanın bir parçası olarak derler ve yayar. Varsayılan olarak olur `true` .
* `RazorCompileOnPublish`: Ne zaman `true` , Razor derlemeyi proje yayımlamanın bir parçası olarak derler ve yayar. Varsayılan olarak olur `true` .

Aşağıdaki tablodaki Özellikler ve öğeler, SDK 'ya giriş ve çıkış yapılandırmak için kullanılır Razor .

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ASP.NET Core 3,0 ' den başlayarak, MVC görünümleri veya Razor sayfaları `RazorCompileOnBuild` `RazorCompileOnPublish` Proje dosyasındaki ya da MSBuild özellikleri devre dışı bırakılmışsa varsayılan olarak sunulmuyor. Uygulamalar [Microsoft. AspNetCore. Mvc öğesine açık bir başvuru eklememelidir. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation)Uygulama *. cshtml* dosyalarını işlemek için çalışma zamanı derlemesini kullanıyorsa runtimecompilation paketi.

::: moniker-end

| Öğeler | Açıklama |
| ----- | ----------- |
| `RazorGenerate` | Kod oluşturmaya giriş olan öğe öğeleri (*. cshtml* dosyaları). |
| `RazorComponent` | Bileşen kodu oluşturmaya giriş olan öğe öğeleri (*. Razor* dosyaları) Razor . |
| `RazorCompile` | Derleme hedeflerine giriş olan öğe öğeleri (*. cs* dosyaları) Razor . `ItemGroup`Derlemeye derlenecek ek dosyaları belirtmek için bunu kullanın Razor . |
| `RazorTargetAssemblyAttribute` | Derleme için öznitelikleri oluşturmak için kullanılan öğe öğeleri Razor . Örnek:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Oluşturulan derlemeye gömülü kaynaklar olarak eklenen öğe öğeleri Razor . |

::: moniker range=">= aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Tarafından üretilen derlemenin dosya adı (uzantısı olmadan) Razor . |
| `RazorOutputPath` | RazorÇıkış dizini. |
| `RazorCompileToolset` | Derlemeyi derlemek için kullanılan araç takımını tespit etmek için kullanılır Razor . Geçerli değerler `Implicit` , `RazorSDK` , ve `PrecompilationTool` . |
| [Enabledefaultcontentıtems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne zaman `true` , *web.config*, *. JSON*ve *. cshtml* dosyalarını projede içerik olarak içerir. Aracılığıyla başvurulduğunda `Microsoft.NET.Sdk.Web` , *Wwwroot* ve yapılandırma dosyaları altındaki dosyalar da dahil edilmiştir. |
| `EnableDefaultRazorGenerateItems` | Ne zaman `true` , öğelerdeki öğelerden *. cshtml* dosyalarını içerir `Content` `RazorGenerate` . |
| `GenerateRazorTargetAssemblyInfo` | Ne zaman `true` , tarafından belirtilen öznitelikleri içeren bir *. cs* dosyası oluşturur `RazorAssemblyAttribute` ve derleme çıkışında dosyasını içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne zaman `true` , ' a bir varsayılan derleme öznitelikleri kümesi ekler `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Ne zaman `true` , `RazorGenerate` öğe (*. cshtml*) dosyalarını yayımlama dizinine kopyalar. Genellikle, derleme Razor zamanında veya yayımlama zamanında derlemeye katılırsanız yayımlanmış bir uygulama için dosyalar gerekli değildir. Varsayılan olarak olur `false` . |
| `PreserveCompilationReferences` | Ne zaman `true` , başvuru derleme öğelerini yayımlama dizinine kopyalayın. Genellikle, derleme Razor zamanında veya yayımlama zamanında derleme gerçekleşirse yayımlanmış bir uygulama için başvuru derlemeleri gerekli değildir. `true`Yayımlanmış uygulamanız çalışma zamanı derlemesi gerektiriyorsa, olarak ayarlayın. Örneğin, `true` uygulama çalışma zamanında *. cshtml* dosyalarını değiştirirse veya gömülü görünümleri kullanıyorsa, değerini olarak ayarlayın. Varsayılan olarak olur `false` . |
| `IncludeRazorContentInPack` | Ne zaman `true` , tüm Razor içerik öğeleri (*. cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlenir. Varsayılan olarak olur `false` . |
| `EmbedRazorGenerateSources` | Ne zaman `true` , Razor oluşturulan derlemeye katıştırılmış dosyalar olarak Generate (*. cshtml*) öğeleri ekler Razor . Varsayılan olarak olur `false` . |
| `UseRazorBuildServer` | Ne zaman `true` , kod oluşturma işinin yükünü boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan değer olarak değeridir `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Ne zaman `true` , SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma ZAMANıNDA MVC tarafından kullanılan ek öznitelikler üretir. |
| `DefaultWebContentItemExcludes` | `Content`Web veya SDK 'yı hedefleyen projelerde öğe grubundan çıkarılacak öğe öğeleri için glob bir model Razor |
| `ExcludeConfigFilesFromBuildOutput` | Ne zaman `true` , *. config* ve *. JSON* dosyaları derleme çıkış dizinine kopyalanmaz. |
| `AddRazorSupportForMvc` | Ne zaman `true` , Razor MVC görünümlerini veya sayfalarını içeren uygulamalar oluştururken gereklı olan MVC yapılandırmasına yönelik destek eklemek için SDK 'yı yapılandırır Razor . Bu özellik, Web SDK 'sını hedefleyen .NET Core 3,0 veya üzeri projeler için örtük olarak ayarlanmıştır |
| `RazorLangVersion` | RazorHedeflenecek dilin sürümü. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Özellik | Açıklama |
| -------- | ----------- |
| `RazorTargetName` | Tarafından üretilen derlemenin dosya adı (uzantısı olmadan) Razor . |
| `RazorOutputPath` | RazorÇıkış dizini. |
| `RazorCompileToolset` | Derlemeyi derlemek için kullanılan araç takımını tespit etmek için kullanılır Razor . Geçerli değerler `Implicit` , `RazorSDK` , ve `PrecompilationTool` . |
| [Enabledefaultcontentıtems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | `true` varsayılan değerdir. Ne zaman `true` , *web.config*, *. JSON*ve *. cshtml* dosyalarını projede içerik olarak içerir. Aracılığıyla başvurulduğunda `Microsoft.NET.Sdk.Web` , *Wwwroot* ve yapılandırma dosyaları altındaki dosyalar da dahil edilmiştir. |
| `EnableDefaultRazorGenerateItems` | Ne zaman `true` , öğelerdeki öğelerden *. cshtml* dosyalarını içerir `Content` `RazorGenerate` . |
| `GenerateRazorTargetAssemblyInfo` | Ne zaman `true` , tarafından belirtilen öznitelikleri içeren bir *. cs* dosyası oluşturur `RazorAssemblyAttribute` ve derleme çıkışında dosyasını içerir. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Ne zaman `true` , ' a bir varsayılan derleme öznitelikleri kümesi ekler `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Ne zaman `true` , `RazorGenerate` öğe (*. cshtml*) dosyalarını yayımlama dizinine kopyalar. Genellikle, derleme Razor zamanında veya yayımlama zamanında derlemeye katılırsanız yayımlanmış bir uygulama için dosyalar gerekli değildir. Varsayılan olarak olur `false` . |
| `CopyRefAssembliesToPublishDirectory` | Ne zaman `true` , başvuru derleme öğelerini yayımlama dizinine kopyalayın. Genellikle, derleme Razor zamanında veya yayımlama zamanında derleme gerçekleşirse yayımlanmış bir uygulama için başvuru derlemeleri gerekli değildir. `true`Yayımlanmış uygulamanız çalışma zamanı derlemesi gerektiriyorsa, olarak ayarlayın. Örneğin, `true` uygulama çalışma zamanında *. cshtml* dosyalarını değiştirirse veya gömülü görünümleri kullanıyorsa, değerini olarak ayarlayın. Varsayılan olarak olur `false` . |
| `IncludeRazorContentInPack` | Ne zaman `true` , tüm Razor içerik öğeleri (*. cshtml* dosyaları) oluşturulan NuGet paketine eklenmek üzere işaretlenir. Varsayılan olarak olur `false` . |
| `EmbedRazorGenerateSources` | Ne zaman `true` , Razor oluşturulan derlemeye katıştırılmış dosyalar olarak Generate (*. cshtml*) öğeleri ekler Razor . Varsayılan olarak olur `false` . |
| `UseRazorBuildServer` | Ne zaman `true` , kod oluşturma işinin yükünü boşaltmak için kalıcı bir yapı sunucusu işlemi kullanır. Varsayılan değer olarak değeridir `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Ne zaman `true` , SDK, uygulama bölümü keşfi gerçekleştirmek için çalışma ZAMANıNDA MVC tarafından kullanılan ek öznitelikler üretir. |
| `DefaultWebContentItemExcludes` | `Content`Web veya SDK 'yı hedefleyen projelerde öğe grubundan çıkarılacak öğe öğeleri için glob bir model Razor |
| `ExcludeConfigFilesFromBuildOutput` | Ne zaman `true` , *. config* ve *. JSON* dosyaları derleme çıkış dizinine kopyalanmaz. |
| `AddRazorSupportForMvc` | Ne zaman `true` , Razor MVC görünümlerini veya sayfalarını içeren uygulamalar oluştururken gereklı olan MVC yapılandırmasına yönelik destek eklemek için SDK 'yı yapılandırır Razor . Bu özellik, Web SDK 'sını hedefleyen .NET Core 3,0 veya üzeri projeler için örtük olarak ayarlanmıştır |
| `RazorLangVersion` | RazorHedeflenecek dilin sürümü. |

::: moniker-end

Özellikler hakkında daha fazla bilgi için bkz. [MSBuild özellikleri](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Hedefler

RazorSDK iki birincil hedefi tanımlar:

* `RazorGenerate`: Kod, öğe öğelerinden *. cs* dosyaları oluşturur `RazorGenerate` . `RazorGenerateDependsOn`Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için özelliğini kullanın.
* `RazorCompile`: İçinde oluşturulan *. cs* dosyalarını derleme için derler Razor . `RazorCompileDependsOn`Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için öğesini kullanın.
* `RazorComponentGenerate`: Kod, öğe öğeleri için *. cs* dosyaları oluşturur `RazorComponent` . `RazorComponentGenerateDependsOn`Bu hedeften önce veya sonra çalışabilecek ek hedefleri belirtmek için özelliğini kullanın.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Görünümlerin çalışma zamanı derlemesi Razor

* Varsayılan olarak SDK, Razor çalışma zamanı derlemesini gerçekleştirmek için gerekli olan başvuru derlemelerini yayımlamaz. Bu durum, uygulama modeli çalışma zamanı derlemesini kullandığında derleme hatalarıyla sonuçlanır &mdash; . uygulama yayımlandıktan sonra uygulama gömülü görünümleri veya değişiklik görünümlerini kullanır. `CopyRefAssembliesToPublishDirectory` `true` Başvuru derlemelerini yayımlamaya devam etmek için olarak ayarlayın.

* Bir Web uygulaması için uygulamanızın SDK 'yı hedeflediğinden emin olun `Microsoft.NET.Sdk.Web` .

## <a name="no-locrazor-language-version"></a>Razordil sürümü

`Microsoft.NET.Sdk.Web`SDK 'yı hedeflerken, Razor dil sürümü uygulamanın hedef Framework sürümünden algılanır. SDK 'yı hedefleyen projeler `Microsoft.NET.Sdk.Razor` veya uygulamanın Razor çıkarılan değerden farklı bir dil sürümü gerektirmesi durumunda, `<RazorLangVersion>` uygulamanın proje dosyasındaki özelliği ayarlanarak bir sürüm yapılandırılabilir:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razordil sürümü, için oluşturulduğu çalışma zamanının sürümü ile sıkı bir şekilde tümleşiktir. Çalışma zamanı için tasarlanmamış bir dil sürümünü hedeflemek desteklenmez ve muhtemelen derleme hataları üretir.

## <a name="additional-resources"></a>Ek kaynaklar

* [.NET Core için csproj biçimine eklemeler](/dotnet/core/tools/csproj)
* [Yaygın MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items)
