---
title: ASP.NET Core kullanmaya başlayınBlazor
author: guardrex
description: Blazor Blazor Tercih ettiğiniz araç ile uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243609"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core kullanmaya başlayınBlazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Kullanmaya başlamak için Blazor , araç seçiminiz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.

1. Yeni bir proje oluşturma.

1. ** Blazor Uygulama**seçin. **İleri**’yi seçin.

1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**'u seçin.

1. BlazorWebassembly deneyimi için ** Blazor Webassembly uygulama** şablonunu seçin. BlazorSunucu deneyimi için ** Blazor sunucu uygulaması** şablonunu seçin. **Oluştur**'u seçin.

   İki Blazor barındırma modeli, * Blazor webassembly* ve * Blazor sunucu*hakkında bilgi için bkz <xref:blazor/hosting-models> ..

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler. SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:

   ```dotnetcli
   dotnet --version
   ```

1. En son [Visual Studio Code](https://code.visualstudio.com/)sürümünü yükler.

1. En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' a `debug.javascript.usePreview` ayarlayın `true` .

  `debug.javascript.usePreview` `true` Vs Code Kullanıcı arabirimini kullanmak üzere ayarlamak için, **Dosya**  >  **tercihleri**  >  **ayarları** ' nı açın ve arama yapın `debug javascript use preview` . **Node.js ve Chrome için yeni bir önizleme Içindeki JavaScript hata ayıklayıcısını kullan**onay kutusunu işaretleyin.

1. Bir Blazor weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   İki Blazor barındırma modeli, * Blazor webassembly* ve * Blazor sunucu*hakkında bilgi için bkz <xref:blazor/hosting-models> ..

1. `WebApplication1`Visual Studio Code klasörü açın.

1. IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister. **Evet**' i seçin.

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.

1. **Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.

1. Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.

   BlazorWebassembly deneyimi için ** Blazor Webassembly uygulama** şablonunu seçin. BlazorSunucu deneyimi için ** Blazor sunucu uygulaması** şablonunu seçin. **İleri**’yi seçin.

   İki Blazor barındırma modeli, * Blazor webassembly* ve * Blazor sunucu*hakkında bilgi için bkz <xref:blazor/hosting-models> ..

1. Aşağıdaki konfigürasyonları onaylayın:

   * **Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.
   * **Kimlik doğrulaması** **yok**olarak ayarlandı.
   
   **İleri**’yi seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `WebApplication1` . **Oluştur**'u seçin.

1. **Run**  >  Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' ı seçin. Uygulamayı **Run**  >  *hata ayıklayıcıyla*çalıştırmak için uygulamayı**Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.

Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. [.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler. SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:

   ```dotnetcli
   dotnet --version
   ```

1. Bir Blazor weelsembly deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   İki Blazor barındırma modeli, * Blazor webassembly* ve * Blazor sunucu*hakkında bilgi için bkz <xref:blazor/hosting-models> ..

1. Bir tarayıcıda öğesine gidin `https://localhost:5001` .

---

Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:

* Giriş Sayfası
* Sayaç
* Verileri getir

Sayaç sayfasında, bir sayfa yenileme olmadan sayacı artırmak için düğmeyi seçin. Bir Web sayfasındaki sayacı normal şekilde artırma, JavaScript yazmayı gerektirir, ancak ile Blazor C# kullanabilirsiniz.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

`/counter`En üstteki yönergeyle belirtilen şekilde tarayıcıda için bir istek `@page` , `Counter` bileşenin içeriğini işlemesine neden olur. Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.

Düğme her seçildiğinde:

* `onclick`Olay tetiklenir.
* `IncrementCount`Yöntemi çağrılır.
* `currentCount`Artırılır.
* Bileşen yeniden işlenir.

Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.

HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin. Örneğin, bileşene `Counter` bir öğe ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin `<Counter />` `Index` .

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uygulamayı çalıştırın. Giriş sayfası, bileşen tarafından sağlanmış kendi sayacıdır `Counter` .

Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components/index#child-content)kullanılarak belirtilir. Bileşene bir parametre eklemek için `Counter` bileşenin `@code` bloğunu güncelleştirin:

* Özniteliği ile için bir public özelliği ekleyin `IncrementAmount` [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .
* `IncrementCount` `IncrementAmount` Değerini değerini artırdığınızda kullanmak için yöntemini değiştirin `currentCount` .

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

`IncrementAmount` `Index` `<Counter>` Özniteliği kullanarak bileşenin öğesinde öğesini belirtin.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uygulamayı çalıştırın. `Index`Bileşenin, düğmenin her seçililişinde on ile artan kendi sayacı vardır. İçindeki `Counter` bileşen ( `Pages/Counter.razor` ), `/counter` bir tane tarafından arttırmaya devam eder.

## <a name="next-steps"></a>Sonraki adımlar

Adım Blazor adım bir uygulama oluşturun:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/templates>
* <xref:signalr/introduction>
