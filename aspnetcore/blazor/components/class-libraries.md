---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin Blazor bir dış bileşen kitaplığından uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944497"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor bileşenleri sınıf kitaplıkları

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
1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. Bu konudaki örneklerde proje adı kullanılır `MyComponentLib1` . **Oluştur**'u seçin.
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

1. Komut kabuğunda komutuyla ** Razor sınıf kitaplığı** şablonu ( `razorclasslib` ) kullanın [`dotnet new`](/dotnet/core/tools/dotnet-new) . Aşağıdaki örnekte adlı bir RCL oluşturulur `MyComponentLib1` . Komut yürütüldüğünde, tutan klasör `MyComponentLib1` otomatik olarak oluşturulur:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
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

Aşağıdaki örneklerde, `MyComponentLib1` bir bileşeni içeren bir bileşen kitaplığı vardır `SalesReport` .

`SalesReport`Bileşene, ad alanı ile tam tür adı kullanılarak başvurulabilir:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Ayrıca, kitaplık bir yönergeyle kapsama alınırsa bileşene de başvurulabilir `@using` :

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

`@using MyComponentLib1` `_Import.razor` Kitaplığın bileşenlerini bir projenin tamamına kullanılabilir hale getirmek için, en üst düzey dosyaya yönergesini ekleyin. `_Import.razor`Ad alanını tek bir sayfaya veya bir klasör içindeki sayfa kümesine uygulamak için herhangi bir düzeydeki bir dosyaya yönerge ekleyin.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>RazorStatik varlıklar içeren bir bileşen sınıfı kitaplığı oluşturma

RCL statik varlıkları içerebilir. Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir. Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>NuGet 'i derleyin, paketleyebilir ve iade edin

Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir. Paketleme, komut [`dotnet pack`](/dotnet/core/tools/dotnet-pack) kabuğu 'nda komutu kullanılarak gerçekleştirilir:

```dotnetcli
dotnet pack
```

Komut kabuğunda komutunu kullanarak paketi NuGet 'e yükleyin [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/ui-class>
* [Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
