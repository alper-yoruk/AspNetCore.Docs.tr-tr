---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin bir dış bileşen kitaplığından Blazor uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 57f3494fd825b6549c40f56962da2c8076e8fd51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767102"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor bileşenleri sınıf kitaplıkları

[Simon Timms](https://github.com/stimms) tarafından

Bileşenler, projeler genelinde [Razor sınıf kitaplığı 'nda (RCL)](xref:razor-pages/ui-class) paylaşılabilir. *Razor bileşenleri sınıf kitaplığı* , şuradan eklenebilir:

* Çözümdeki başka bir proje.
* Bir NuGet paketi.
* Başvurulan bir .NET kitaplığı.

Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.

## <a name="create-an-rcl"></a>RCL oluşturma

Ortamınızı Blazor için yapılandırmak üzere <xref:blazor/get-started> makalesindeki yönergeleri izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. **Razor sınıfı kitaplığı**' nı seçin. **İleri**’yi seçin.
1. **Yeni bir Razor sınıf kitaplığı oluştur** Iletişim kutusunda **Oluştur**' u seçin.
1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. Bu konudaki örneklerde proje adı `MyComponentLib1`kullanılır. **Oluştur**’u seçin.
1. RCL 'yi bir çözüme ekleyin:
   1. Çözüme sağ tıklayın. **Varolan proje** **Ekle** > ' yi seçin.
   1. RCL 'nin proje dosyasına gidin.
   1. RCL 'nin proje dosyasını (*. csproj*) seçin.
1. Uygulamadan RCL 'ye bir başvuru ekleyin:
   1. Uygulama projesine sağ tıklayın. Başvuru **Ekle** > **Reference**' yi seçin.
   1. RCL projesini seçin. **Tamam**’ı seçin.

> [!NOTE]
> Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu Işaretliyse, Razor bileşeni yazma özelliğini etkinleştirmek için aşağıdaki içeriklerle oluşturulan projenin köküne bir *_Imports. Razor* dosyası da ekleyin:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Dosyayı oluşturulan projenin köküne el ile ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Bir komut kabuğunda [DotNet New](/dotnet/core/tools/dotnet-new) komutuyla **Razor sınıf kitaplığı** şablonunu (`razorclasslib`) kullanın. Aşağıdaki örnekte adlı `MyComponentLib1`bir RCL oluşturulur. Komut yürütüldüğünde, tutan `MyComponentLib1` klasör otomatik olarak oluşturulur:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Şablondan RCL oluşturulurken `-s|--support-pages-and-views` anahtar kullanılıyorsa, Razor bileşeni yazma özelliğini etkinleştirmek için aşağıdaki içeriklerle oluşturulan projenin köküne bir *_Imports. Razor* dosyası da ekleyin:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Dosyayı oluşturulan projenin köküne el ile ekleyin.

1. Kitaplığı var olan bir projeye eklemek için, bir komut kabuğunda [DotNet Add Reference](/dotnet/core/tools/dotnet-add-reference) komutunu kullanın. Aşağıdaki örnekte, RCL uygulamaya eklenir. Uygulamanın proje klasöründen, kitaplığın yolunu kullanarak aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Kitaplık bileşeni kullanma

Başka bir projedeki bir kitaplıkta tanımlanan bileşenleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:

* Ad alanı ile tam tür adını kullanın.
* Using yönergesini kullanın Razor. [ \@](xref:mvc/views/razor#using) Tek tek bileşenler, ada göre eklenebilir.

Aşağıdaki örneklerde, `MyComponentLib1` bir `SalesReport` bileşeni içeren bir bileşen kitaplığı vardır.

Bileşene `SalesReport` , ad alanı ile tam tür adı kullanılarak başvurulabilir:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Ayrıca, kitaplık bir `@using` yönergeyle kapsama alınırsa bileşene de başvurulabilir:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Kitaplığın bileşenlerini `@using MyComponentLib1` bir projenin tamamına kullanılabilir hale getirmek için üst düzey *_Import. Razor* dosyasına yönergesini ekleyin. Ad alanını tek bir sayfaya veya bir klasör içindeki sayfa kümesine uygulamak için herhangi bir düzeydeki bir *_Import. Razor* dosyasına yönergesini ekleyin.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Statik varlıklar Razor içeren bir bileşen sınıfı kitaplığı oluşturma

RCL statik varlıkları içerebilir. Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir. Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>NuGet 'i derleyin, paketleyebilir ve iade edin

Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir. Paketleme, bir komut kabuğu 'nda [DotNet Pack](/dotnet/core/tools/dotnet-pack) komutu kullanılarak gerçekleştirilir:

```dotnetcli
dotnet pack
```

Bir komut kabuğunda [DotNet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) komutunu kullanarak paketi NuGet 'e yükleyin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/ui-class>
* [Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
