---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin Blazor bir dış bileşen kitaplığından uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 0bace66e0aab41cf31e18fe9f86dbf9bbcf59447
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014704"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>ASP.NET Core Razor bileşenleri sınıf kitaplıkları

[Simon Timms](https://github.com/stimms) tarafından

Bileşenler, projeler genelinde bir [ Razor sınıf kitaplığı 'NDA (RCL)](xref:razor-pages/ui-class) paylaşılabilir. Bir * Razor bileşen sınıfı kitaplığı* , şuradan eklenebilir:

* Çözümdeki başka bir proje.
* Bir NuGet paketi.
* Başvurulan bir .NET kitaplığı.

Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.

## <a name="create-an-rcl"></a>RCL oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. ** Razor Sınıf kitaplığı**' nı seçin. **İleri**’yi seçin.
1. **Yeni Razor sınıf kitaplığı oluştur** iletişim kutusunda **Oluştur**' u seçin.
1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. Bu konudaki örneklerde proje adı kullanılır `ComponentLibrary` . **Oluştur**’u seçin.
1. RCL 'yi bir çözüme ekleyin:
   1. Çözüme sağ tıklayın. **Add**  >  **Varolan proje**Ekle ' yi seçin.
   1. RCL 'nin proje dosyasına gidin.
   1. RCL 'nin proje dosyasını () seçin `.csproj` .
1. Uygulamadan RCL 'ye bir başvuru ekleyin:
   1. Uygulama projesine sağ tıklayın. Başvuru **Ekle**' yi seçin  >  **Reference**.
   1. RCL projesini seçin. **Tamam**’ı seçin.

> [!NOTE]
> Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu işaretliyse, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne da bir dosya ekleyin Razor :
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Dosyayı oluşturulan projenin köküne el ile ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Komut kabuğunda komutuyla ** Razor sınıf kitaplığı** şablonu ( `razorclasslib` ) kullanın [`dotnet new`](/dotnet/core/tools/dotnet-new) . Aşağıdaki örnekte adlı bir RCL oluşturulur `ComponentLibrary` . Komut yürütüldüğünde, tutan klasör `ComponentLibrary` otomatik olarak oluşturulur:

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

::: moniker range=">= aspnetcore-5.0"

`Component1` `my-component` Bileşene CSS sınıfı sağlamak için, ' deki Framework [ `Link` bileşeni](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) kullanılarak kitaplığın stil sayfasına bağlanın `Component1.razor` :

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Uygulama genelinde stil sayfasını sağlamak için, bunun yerine uygulamanın `wwwroot/index.html` dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanabilirsiniz Blazor Server :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

`Link`Bileşen bir alt bileşende kullanıldığında, bileşenin alt öğesi işlendiği sürece bağlantılı varlık ana bileşenin diğer alt bileşenleri için de kullanılabilir `Link` . `Link`Bir alt bileşende bileşeni kullanma ve veya ' a bir HTML etiketi yerleştirme arasındaki ayrım, `<link>` `wwwroot/index.html` `Pages/_Host.cshtml` çerçeve bileşeninin işlenmiş html etiketinin:

* , Uygulama durumu ile değiştirilebilir. Sabit kodlanmış `<link>` HTML etiketi uygulama durumu tarafından değiştirilemez.
* `<head>`Üst bileşen artık IŞLENMEDIĞINDE HTML 'den kaldırılır.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Component1` `my-component` CSS sınıfını sağlamak için, uygulamanın `wwwroot/index.html` dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanın Blazor Server :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>RazorStatik varlıklar içeren bir bileşen sınıfı kitaplığı oluşturma

RCL statik varlıkları içerebilir. Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir. Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Birden çok barındırılan uygulamaya bileşenleri ve statik varlıkları sağlama Blazor

Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

## <a name="build-pack-and-ship-to-nuget"></a>NuGet 'i derleyin, paketleyebilir ve iade edin

Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir. Paketleme, komut [`dotnet pack`](/dotnet/core/tools/dotnet-pack) kabuğu 'nda komutu kullanılarak gerçekleştirilir:

```dotnetcli
dotnet pack
```

Komut kabuğunda komutunu kullanarak paketi NuGet 'e yükleyin [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/ui-class>
* [Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
