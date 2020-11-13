---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin Blazor bir dış bileşen kitaplığından uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 24a5b93a18cfe36c50d9739ba56d12aca41615c0
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570165"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>ASP.NET Core Razor bileşenleri sınıf kitaplıkları

[Simon Timms](https://github.com/stimms) tarafından

Bileşenler, projeler genelinde bir [ Razor sınıf kitaplığı 'NDA (RCL)](xref:razor-pages/ui-class) paylaşılabilir. Bir *Razor bileşen sınıfı kitaplığı* , şuradan eklenebilir:

* Çözümdeki başka bir proje.
* Bir NuGet paketi.
* Başvurulan bir .NET kitaplığı.

Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.

## <a name="create-an-rcl"></a>RCL oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. **Razor Sınıf kitaplığı** ' nı seçin. **İleri** ’yi seçin.
1. **Yeni Razor sınıf kitaplığı oluştur** iletişim kutusunda **Oluştur** ' u seçin.
1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. Bu konudaki örneklerde proje adı kullanılır `ComponentLibrary` . **Oluştur** ’u seçin.
1. RCL 'yi bir çözüme ekleyin:
   1. Çözüme sağ tıklayın. **Add**  >  **Varolan proje** Ekle ' yi seçin.
   1. RCL 'nin proje dosyasına gidin.
   1. RCL 'nin proje dosyasını () seçin `.csproj` .
1. Uygulamadan RCL 'ye bir başvuru ekleyin:
   1. Uygulama projesine sağ tıklayın. Başvuru **Ekle** ' yi seçin  >  **Reference**.
   1. RCL projesini seçin. **Tamam** ’ı seçin.

> [!NOTE]
> Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu işaretliyse, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne da bir dosya ekleyin Razor :
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Dosyayı oluşturulan projenin köküne el ile ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Komut kabuğunda komutuyla **Razor sınıf kitaplığı** şablonu ( `razorclasslib` ) kullanın [`dotnet new`](/dotnet/core/tools/dotnet-new) . Aşağıdaki örnekte adlı bir RCL oluşturulur `ComponentLibrary` . Komut yürütüldüğünde, tutan klasör `ComponentLibrary` otomatik olarak oluşturulur:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > `-s|--support-pages-and-views`Şablondan RCL oluşturulurken anahtar kullanılıyorsa, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne bir dosya ekleyin Razor :
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Dosyayı oluşturulan projenin köküne el ile ekleyin.

1. Kitaplığı var olan bir projeye eklemek için komut [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) kabuğu 'nda komutunu kullanın. Aşağıdaki örnekte, RCL uygulamaya eklenir. Uygulamanın proje klasöründen, kitaplığın yolunu kullanarak aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Kitaplık bileşeni kullanma

Başka bir projedeki bir kitaplıkta tanımlanan bileşenleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:

* Ad alanı ile tam tür adını kullanın.
* Kullanım Razor [`@using`](xref:mvc/views/razor#using) yönergesini kullanın. Tek tek bileşenler, ada göre eklenebilir.

Aşağıdaki örneklerde, `ComponentLibrary` bileşeni () içeren bir bileşen kitaplığı vardır `Component1` `Component1.razor` . `Component1`Bileşen, kitaplık oluşturulduğunda RCL proje şablonu tarafından otomatik olarak eklenen örnek bir bileşendir.

`Component1`Bileşene ad alanını kullanarak başvurun:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Alternatif olarak, kitaplığı bir yönergeyle kapsama taşıyın [`@using`](xref:mvc/views/razor#using) ve bileşeni ad alanı olmadan kullanın:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

İsteğe bağlı olarak, `@using ComponentLibrary` `_Import.razor` kitaplığın bileşenlerini bir projenin tamamına kullanılabilir hale getirmek için en üst düzey dosyaya yönergesini dahil edin. `_Import.razor`Ad alanını tek bir bileşene veya bir klasör içindeki bileşen kümesine uygulamak için herhangi bir düzeydeki bir dosyaya yönerge ekleyin.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

`Component1` `my-component` CSS sınıfını sağlamak için, uygulamanın `wwwroot/index.html` dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanın Blazor Server :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>RazorStatik varlıklar içeren bir bileşen sınıfı kitaplığı oluşturma

RCL statik varlıkları içerebilir. Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir. Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Birden çok barındırılan uygulamaya bileşenleri ve statik varlıkları sağlama Blazor

Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>İçin tarayıcı uyumluluk Çözümleyicisi Blazor WebAssembly

Blazor WebAssembly uygulamalar tam .NET API yüzey alanını hedefletir, ancak tüm .NET API 'Leri, tarayıcı korumalı alan kısıtlamaları nedeniyle WebAssembly üzerinde desteklenmez. <xref:System.PlatformNotSupportedException>WebAssembly üzerinde çalışırken desteklenmeyen API 'ler oluşturur. Uygulama, uygulamanın hedef platformları tarafından desteklenmeyen API 'Ler kullandığında, bir platform uyumluluğu Çözümleyicisi geliştiriciyi uyarır. Blazor WebAssemblyUygulamalar için bu, API 'lerin tarayıcılarda desteklendiği anlamına gelir. Uyumluluk Çözümleyicisi için .NET Framework API 'Lerine açıklama ekleme işlemi devam eder, bu nedenle tüm .NET Framework API 'SI Şu anda açıklanmamaktadır.

Blazor WebAssemblyve Razor sınıf kitaplığı projeleri *automatically* `browser` , MSBuild öğesiyle desteklenen bir platform olarak ekleyerek tarayıcı uyumluluk denetimlerini otomatik olarak etkinleştirir `SupportedPlatform` . Kitaplık geliştiricileri, `SupportedPlatform` özelliği etkinleştirmek için öğeyi bir kitaplığın proje dosyasına el ile ekleyebilir:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Bir kitaplık yazarken belirli bir API 'nin şu şekilde belirterek tarayıcılarda desteklenmediğini belirtin `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Daha fazla bilgi için bkz. [belirli platformlarda API 'Leri açıklama ekleme (DotNet/Designs GitHub deposu](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript yalıtımı ve nesne başvuruları

Blazor Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez. JavaScript yalıtımı aşağıdaki avantajları sağlar:

* İçeri aktarılan JavaScript artık genel ad alanını pollutes.
* Kitaplık ve bileşenlerin tüketicileri, ilgili JavaScript 'ı el ile içeri aktarmak için gerekli değildir.

Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>NuGet 'i derleyin, paketleyebilir ve iade edin

Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir. Paketleme, komut [`dotnet pack`](/dotnet/core/tools/dotnet-pack) kabuğu 'nda komutu kullanılarak gerçekleştirilir:

```dotnetcli
dotnet pack
```

Komut kabuğunda komutunu kullanarak paketi NuGet 'e yükleyin [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) .

## <a name="additional-resources"></a>Ek kaynaklar

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Kitaplığa bir XML ara dili (IL) ayarlayıcısı yapılandırma dosyası ekleme](xref:blazor/host-and-deploy/configure-trimmer)
* [Sınıf kitaplıkları ile CSS yalıtım desteği Razor](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Kitaplığa bir XML ara dili (IL) bağlayıcı yapılandırma dosyası ekleme](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
