---
title: ASP.NET Core için araç oluşturmaBlazor
author: guardrex
description: Uygulama derlemek için kullanılabilen araç hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944966"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a>ASP.NET Core için araç oluşturmaBlazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Platformunuz için araçları seçin:

* Windows: **Visual Studio** sekmesini seçin.
* Linux: **Visual Studio Code** sekmesini seçin.
* macOS: **Mac için Visual Studio** sekmesini seçin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.

1. Yeni bir proje oluşturma.

1. ** Blazor Uygulama**seçin. **İleri**’yi seçin.

1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**'u seçin.

1. Bir Blazor WebAssembly deneyim için ** Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için ** Blazor Server uygulama** şablonunu seçin. **Oluştur**'u seçin.

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler. SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:

   ```dotnetcli
   dotnet --version
   ```

1. En son [Visual Studio Code](https://code.visualstudio.com/)sürümünü yükler.

1. En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' a `debug.javascript.usePreview` ayarlayın `true` .

   `debug.javascript.usePreview` `true` Vs Code Kullanıcı arabirimini kullanmak üzere ayarlamak için, **Dosya**  >  **tercihleri**  >  **ayarları** ' nı açın ve arama yapın `debug javascript use preview` . **Node.js ve Chrome için yeni bir önizleme Içindeki JavaScript hata ayıklayıcısını kullan**onay kutusunu işaretleyin.

1. Bir Blazor WebAssembly deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. `WebApplication1`Visual Studio Code klasörü açın.

1. IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister. **Evet**' i seçin.

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.

1. **Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.

1. Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.

   Bir Blazor WebAssembly deneyim için ** Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için ** Blazor Server uygulama** şablonunu seçin. **İleri**’yi seçin.

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. Aşağıdaki konfigürasyonları onaylayın:

   * **Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.
   * **Kimlik doğrulaması** **yok**olarak ayarlandı.
   
   **İleri**’yi seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `WebApplication1` . **Oluştur**'u seçin.

1. **Run**  >  Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' ı seçin. Uygulamayı **Run**  >  *hata ayıklayıcıyla*çalıştırmak için uygulamayı**Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.

Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
