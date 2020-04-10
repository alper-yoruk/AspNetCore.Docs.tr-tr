---
title: ASP.NET Core ile başlayınBlazor
author: guardrex
description: Seçtiğiniz araç Blazor ile Blazor bir uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002918"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Core BlazorASP.NET ile başlayın

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor'a başlamak için, araç lama seçiminiz için kılavuzu izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Blazor Server uygulamaları oluşturmak için [Visual Studio 2019'un](https://visualstudio.microsoft.com/downloads/) en son sürümünü ASP.NET ve web geliştirme iş yüküyle birlikte **yükleyin.**

   Blazor Server ve Blazor WebAssembly uygulamaları oluşturmak için [Visual Studio 2019'un](https://visualstudio.microsoft.com/vs/preview/) en son önizlemesini ASP.NET ve web geliştirme iş yüküyle birlikte **yükleyin.**

   İki Blazor barındırma modelleri hakkında bilgi için, *Blazor WebAssembly* ve *Blazor Server*, bkz. <xref:blazor/hosting-models>

1. Yeni bir proje oluşturma.

1. **Blazor Uygulamasını**seçin. **Sonraki'ni**seçin.

1. **Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum sağlayın. **Oluştur'u**seçin.

1. Blazor WebAssembly deneyimi (Visual Studio 16.6 Preview 2 veya sonraki) için **Blazor WebAssembly Uygulaması** şablonu'nu seçin. Blazor Server deneyimi (Visual Studio 16.4 veya sonrası) için **Blazor Server App** şablonunu seçin. **Oluştur'u**seçin.

1. Uygulamayı çalıştırmak için <kbd>Ctrl</kbd>+<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3.1 SDK'yı](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükleyin.

1. İsteğe bağlı olarak aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükleyin:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > [.NET Core SDK sürüm 3.1.201 veya sonraki](https://dotnet.microsoft.com/download/dotnet-core/3.1) 3.2 Önizleme 3 Blazor WebAssembly şablonu kullanmak için **gereklidir.** Yüklü .NET Core SDK sürümünü `dotnet --version` komut kabuğunda çalıştırarak onaylayın.

1. [Visual Studio Kodunu](https://code.visualstudio.com/)Yükleyin.

1. Visual Studio Code uzantısı için en son [C#'ı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript Hata Ayıklayıcı (Gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` ' olarak `true`ayarlayın.

1. Blazor Server deneyimi için aşağıdaki komutu bir komut kabuğunda uygulayın:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Blazor WebAssembly deneyimi için, komut kabuğunda aşağıdaki komutu uygulayın:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   İki Blazor barındırma modelleri hakkında bilgi için, *Blazor* Server <xref:blazor/hosting-models>ve *Blazor WebAssembly*, bkz.

1. Visual Studio Code'da *WebApplication1* klasörünü açın.

1. IDE, projeyi oluşturmak ve hata ayıklamak için varlık eklemenizi ister. **Evet'i**seçin.

1. Blazor Server ile uygulamayı Visual Studio Code hata ayıklayıcısını kullanarak çalıştırın.

   Blazor WebAssembly ile uygulamayı **.NET Core Launch (Blazor Standalone)** başlatma yapılandırmasını kullanarak başlatın ve chrome başlatma **yapılandırmasındaki .NET Core Debug Blazor Web Assembly'i** kullanarak tarayıcıyı başlatın (Chrome gerektirir). Daha fazla bilgi için bkz. <xref:blazor/debug#visual-studio-code>.

1. Tarayıcıda, `https://localhost:5001`''

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Blazor Server, Visual Studio for Mac'te desteklenir. Blazor WebAssembly şu anda desteklenmez. MacOS'ta Blazor WebAssembly uygulamaları oluşturmak için **.NET Core CLI** sekmesindeki kılavuzu izleyin.

1. [Mac için Visual Studio'u](https://visualstudio.microsoft.com/vs/mac/)yükleyin.

1. **Dosya** > **Yeni Çözüm'ünü** seçin veya Yeni Bir **Proje**oluşturun.

1. Kenar çubuğunda **.NET Core** > **App'ı**seçin.

1. **Blazor Server App** şablonu'nu seçin. **Oluştur'u**seçin.

   Blazor Server barındırma modeli hakkında <xref:blazor/hosting-models>bilgi için bkz.

1. Hedef **Çerçeveyi** **.NET Core 3.1** olarak ayarlayın ve **İleri'yi**seçin.

1. Proje **Adı** alanında, uygulamayı `WebApplication1`adlandırın. **Oluştur'u**seçin.

1. *Hata ayıklama olmadan*uygulamayı çalıştırmak için**Hata Ayıklama olmadan** **Çalıştır'ı** > seçin. Uygulamayı *hata ayıklama yla*çalıştırmak için Uygulamayı Başlat **Hata Ayıklama** ile çalıştırın.

Geliştirme sertifikasına güvenen bir istem görünüyorsa, sertifikaya güvenin ve devam edin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. [.NET Core 3.1 SDK'yı](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükleyin.

1. İsteğe bağlı olarak aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükleyin:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > [.NET Core SDK sürüm 3.1.201 veya sonraki](https://dotnet.microsoft.com/download/dotnet-core/3.1) 3.2 Önizleme 3 Blazor WebAssembly şablonu kullanmak için **gereklidir.** Yüklü .NET Core SDK sürümünü `dotnet --version` komut kabuğunda çalıştırarak onaylayın.

1. Blazor Server deneyimi için, komut kabuğunda aşağıdaki komutları uygulayın:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor WebAssembly deneyimi için, komut kabuğunda aşağıdaki komutları uygulayın:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   İki Blazor barındırma modelleri hakkında bilgi için, *Blazor* Server <xref:blazor/hosting-models>ve *Blazor WebAssembly*, bkz.

1. Tarayıcıda, `https://localhost:5001`''

---

Kenar çubuğundaki sekmelerden birden çok sayfa bulunur:

* Ev
* Sayaç
* Veri getir

Sayaç sayfasında, sayfa yenilemeden sayacı niçin artıya doğru artıya tıklayın **düğmesini** seçin. Bir web sayfasında ki sayacı niçin artıya basması Blazor normalde JavaScript yazmayı gerektirir, ancak c#'ı kullanabilirsiniz.

*Sayfalar/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

`/counter` Üstteki `@page` yönergede belirtildiği gibi tarayıcıdaki istek, bileşenin `Counter` içeriğini işlemesine neden olur. Bileşenler, kullanıcı arasını esnek ve verimli bir şekilde güncelleştirmek için kullanılabilecek render ağacının bellek içi gösterimine dönüşür.

**Beni Tıklat** düğmesi her seçildiğinde:

* Olay `onclick` ateşlendi.
* Yöntem `IncrementCount` denir.
* Bu `currentCount` artış.
* Bileşen yeniden işlenir.

Çalışma süresi, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesnesi Modeli'ne (DOM) uygular.

HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin. Örneğin, `Index` bileşene `Counter` bir `<Counter />` öğe ekleyerek bileşeni uygulamanın ana sayfasına ekleyin.

*Sayfalar/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uygulamayı çalıştırın. Ana sayfanın `Counter` bileşen tarafından sağlanan kendi sayacı vardır.

Bileşen parametreleri öznitelikleri veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir , hangi alt bileşeni özellikleri ayarlamak için izin verir. Bileşene `Counter` bir parametre eklemek için bileşenin `@code` bloğunu güncelleştirin:

* Bir `[Parameter]` öznitelik `IncrementAmount` ile bir kamu malı ekleyin.
* Değerini `IncrementCount` `IncrementAmount` artırırken kullanmak için yöntemi `currentCount`değiştirin.

*Sayfalar/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Bir `IncrementAmount` öznitelik `Index` kullanarak `<Counter>` bileşenin öğesini belirtin.

*Sayfalar/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uygulamayı çalıştırın. Bileşenin `Index` kendi sayacı vardır ve **her tıkla düğmesi** seçildiğinde on artış sağlar. Bileşen `Counter` *(Counter.razor*) `/counter` bir artış devam ediyor.

## <a name="next-steps"></a>Sonraki adımlar

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/templates>
* <xref:signalr/introduction>
