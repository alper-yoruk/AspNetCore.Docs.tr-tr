---
title: ASP.NET Core Razor bileşenleri sınıf kütüphaneleri
author: guardrex
description: Bileşenlerin harici bileşen kitaplığından uygulamalara Blazor nasıl eklenebilir olduğunu keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218772"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor bileşenleri sınıf kütüphaneleri

Yazar: [Simon Timms](https://github.com/stimms)

Bileşenler, projeler arasında [Bir Razor sınıf kitaplığında (RCL)](xref:razor-pages/ui-class) paylaşılabilir. *Razor bileşenleri sınıf kitaplığı* şunları arasından eklenebilir:

* Çözümde başka bir proje.
* Bir NuGet paketi.
* Başvurulan .NET kitaplığı.

Bileşenler normal .NET türleri olduğu gibi, RCL tarafından sağlanan bileşenler de normal .NET derlemeleridir.

## <a name="create-an-rcl"></a>RCL oluşturma

Blazor için <xref:blazor/get-started> ortamınızı yapılandırmak için makaledeki kılavuzu izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. **Razor Class Kitaplığı'nı**seçin. **Sonraki'ni**seçin.
1. Yeni **bir Razor sınıfı kitaplık oluştur** iletişim kutusunda **Oluştur'u**seçin.
1. **Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin. Bu konudaki örneklerde proje `MyComponentLib1`adı kullanılır. **Oluştur'u**seçin.
1. RCL'yi çözüme ekleyin:
   1. Çözüme sağ tıklayın. **Varolan Proje** **Ekle'yi** > seçin.
   1. RCL'nin proje dosyasına gidin.
   1. RCL'nin proje dosyasını seçin (*.csproj*).
1. Uygulamadan RCL bir başvuru ekleyin:
   1. Uygulama projesine sağ tıklayın. **Referans** **Ekle'yi** > seçin.
   1. RCL projesini seçin. **Tamam'ı**seçin.

> [!NOTE]
> Şablondan RCL oluştururken **Destek sayfaları ve görünümleri** onay kutusu seçilirse, razor bileşeninin yazılmasını etkinleştirmek için oluşturulan projenin köküne aşağıdaki içeriklerle bir *_Imports.razor* dosyası da ekleyin:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Dosyayı oluşturulan projenin kökünü el ile ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. Bir komut kabuğunda`razorclasslib` [dotnet yeni](/dotnet/core/tools/dotnet-new) komutu ile Razor Class **Library** şablonunu ( ) kullanın. Aşağıdaki örnekte, bir RCL `MyComponentLib1`adlı oluşturulur. Komut yürütüldüğünde tutan `MyComponentLib1` klasör otomatik olarak oluşturulur:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Şablondan `-s|--support-pages-and-views` RCL oluştururken anahtar kullanılırsa, Razor bileşeninin yazılmasını etkinleştirmek için oluşturulan projenin köküne bir *_Imports.razor* dosyası da ekleyin:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Dosyayı oluşturulan projenin kökünü el ile ekleyin.

1. Kitaplığı varolan bir projeye eklemek için, komut kabuğundaki [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) komutunu kullanın. Aşağıdaki örnekte, RCL uygulamaya eklenir. Uygulamanın proje klasöründen kitaplık yolunda aşağıdaki komutu uygulayın:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Kitaplık bileşenini tüketin

Başka bir projede kitaplıkta tanımlanan bileşenleri tüketmek için aşağıdaki yaklaşımlardan birini kullanın:

* Ad alanı yla tam tür adını kullanın.
* Razor'ın direktifini [ \@kullan.](xref:mvc/views/razor#using) Tek tek bileşenler ada göre eklenebilir.

Aşağıdaki örneklerde, `MyComponentLib1` bir bileşen içeren `SalesReport` bir bileşen kitaplığı dır.

Bileşen, `SalesReport` ad alanı ile tam tür adı kullanılarak başvurulabilir:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Kitaplık bir `@using` yönergeyle kapsam haline getirilirse bileşene de başvurulabilir:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Kitaplığın `@using MyComponentLib1` bileşenlerini tüm proje için kullanılabilir hale getirmek için yönergeleri üst düzey *_Import.jilet* dosyasına ekleyin. Ad alanını bir klasördeki tek bir sayfaya veya sayfa kümesine uygulamak için yönergeleri herhangi bir düzeyde *ki _Import.razor* dosyasına ekleyin.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Statik varlıklarla bir Razor bileşenleri sınıf kitaplığı oluşturma

Bir RCL statik varlıkları içerebilir. Statik varlıklar kitaplığı tüketen herhangi bir uygulama için kullanılabilir. Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>NuGet'e oluşturun, paketlein ve gönderiyapın

Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, bunları NuGet'e paketlemek ve göndermenin herhangi bir kitaplığı Paketlemeve NuGet'e göndermesinden hiçbir farkı yoktur. Paketleme komut kabuğundaki [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu kullanılarak gerçekleştirilir:

```dotnetcli
dotnet pack
```

Paketi bir komut kabuğundaki [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) komutunu kullanarak NuGet'e yükleyin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/ui-class>
* [Kitaplık için XML bağlayıcı yapılandırma dosyası ekleme](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
