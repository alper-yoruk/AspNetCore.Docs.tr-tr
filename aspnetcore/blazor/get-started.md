---
title: ASP.NET Core kullanmaya başlayınBlazor
author: guardrex
description: Tercih ettiğiniz araç Blazor ile Blazor uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111077"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor kullanmaya başlama

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor kullanmaya başlamak için araç seçiminiz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Blazor Server uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yüklemelisiniz.

   Blazor Server ve Blazor WebAssembly uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) ' in en son önizlemesini yüklersiniz.

   İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..

1. Aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yüklemelisiniz:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Yeni bir proje oluşturma.

1. **Blazor uygulamasını**seçin. **İleri**’yi seçin.

1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**’u seçin.

1. Blazor WebAssembly deneyimi için (Visual Studio 16,6 Preview 2 veya üzeri), **Blazor Webassembly uygulama** şablonunu seçin. Bir Blazor Server deneyimi için (Visual Studio 16,4 veya üzeri) **Blazor Server uygulama** şablonunu seçin. **Oluştur**’u seçin.

1. Uygulamayı çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.

1. İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükler:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > 3,2 Preview 4 Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** . Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.

1. [Visual Studio Code](https://code.visualstudio.com/)'i yükler.

1. En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' `debug.javascript.usePreview` a ayarlayın `true`.

1. Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Bir Blazor WebAssembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>..

1. Visual Studio Code 'de *WebApplication1* klasörünü açın.

1. IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister. **Evet**' i seçin.

1. Blazor sunucusu ile Visual Studio Code hata ayıklayıcıyı kullanarak uygulamayı çalıştırın.

   Blazor WebAssembly ile, **.NET Core başlatma (Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın ve ardından Chrome başlatma yapılandırması **'Nda .NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir). Daha fazla bilgi için bkz. <xref:blazor/debug#visual-studio-code>.

1. Bir tarayıcıda öğesine `https://localhost:5001`gidin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Blazor sunucusu Mac için Visual Studio desteklenir. Blazor WebAssembly Şu anda desteklenmiyor. MacOS 'ta Blazor WebAssembly uygulamaları derlemek için **.NET Core CLI** sekmesindeki yönergeleri izleyin.

1. [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.

1. **Dosya** > **yeni çözüm** ' ı seçin veya **Yeni bir proje**oluşturun.

1. Kenar çubuğunda **.NET Core** > **uygulaması**' nı seçin.

1. **Blazor Server uygulama** şablonunu seçin. **Oluştur**’u seçin.

   Blazor sunucusu barındırma modeli hakkında daha fazla bilgi için bkz <xref:blazor/hosting-models>..

1. **Hedef Framework 'ü** **.NET Core 3,1** olarak ayarlayın ve **İleri ' yi**seçin.

1. **Proje adı** alanında, uygulamayı `WebApplication1`adlandırın. **Oluştur**’u seçin.

1. Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan** **Çalıştır ' ı seçin.** >  Uygulamayı hata *ayıklayıcıyla*çalıştırmak Için, **hata ayıklamayı Başlat** ile uygulamayı çalıştırın.

Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. [.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.

1. İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükler:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > 3,2 Preview 4 Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** . Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.

1. Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Bir Blazor Weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>..

1. Bir tarayıcıda öğesine `https://localhost:5001`gidin.

---

Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:

* Giriş
* Sayaç
* Verileri getir

Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin. Bir Web sayfasındaki sayacı normal şekilde artırma, JavaScript yazmayı gerektirir, ancak Blazor ile C# kullanabilirsiniz.

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

En üstteki `/counter` `@page` yönergeyle belirtilen şekilde tarayıcıda için bir istek, `Counter` bileşenin içeriğini işlemesine neden olur. Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.

**Bana tıklama** düğmesi her seçildiğinde:

* `onclick` Olay tetiklenir.
* `IncrementCount` Yöntemi çağrılır.
* `currentCount` Artırılır.
* Bileşen yeniden işlenir.

Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.

HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin. Örneğin, bileşene bir `Counter` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin.

*Pages/Index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uygulamayı çalıştırın. Giriş sayfası, `Counter` bileşen tarafından sağlanmış kendi sayacıdır.

Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir. `Counter` Bileşene bir parametre eklemek için bileşenin `@code` bloğunu güncelleştirin:

* `[Parameter]` Özniteliği ile için `IncrementAmount` bir public özelliği ekleyin.
* Değerini değerini `IncrementCount` `IncrementAmount` artırdığınızda kullanmak için yöntemini değiştirin `currentCount`.

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

`IncrementAmount` Özniteliği kullanarak `Index` bileşenin `<Counter>` öğesinde öğesini belirtin.

*Pages/Index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uygulamayı çalıştırın. `Index` Bileşenin, **bana tıklama** düğmesi seçildiğinde her seferinde on ile artan kendi sayacı vardır. ' `Counter` De `/counter` bileşen (*Counter. Razor*), bir tane tarafından arttırmaya devam eder.

## <a name="next-steps"></a>Sonraki adımlar

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/templates>
* <xref:signalr/introduction>
