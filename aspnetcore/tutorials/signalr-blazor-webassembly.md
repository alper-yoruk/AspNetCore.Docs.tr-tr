---
title: İle ASP.NET Core kullanma SignalRBlazor WebAssembly
author: guardrex
description: İle ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508181"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>İle ASP.NET Core kullanma SignalRBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu öğretici ile kullanarak gerçek zamanlı bir uygulama oluşturma hakkında temel bilgileri öğretir SignalR Blazor WebAssembly . Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Blazor WebAssemblyBarındırılan uygulama projesi oluşturma
> * SignalRİstemci kitaplığını ekleme
> * Hub ekleme SignalR
> * SignalRHub için hizmetler ve uç nokta ekleme SignalR
> * RazorSohbet için bileşen kodu ekle

Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,8 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [Mac için Visual Studio Sürüm 8,8 veya üzeri](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,6 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [Mac için Visual Studio Sürüm 8,6 veya üzeri](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Barındırılan Blazor WebAssembly uygulama projesi oluşturma

Araç seçiminiz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16,8 veya üzeri ve .NET Core SDK 5.0.0 veya üzeri gereklidir.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.

::: moniker-end

1. Yeni bir proje oluşturma.

1. **Blazor Uygulama** ' yı seçin ve **İleri ' yi** seçin.

1. `BlazorSignalRApp` **Proje adı** alanına yazın. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur** ’u seçin.

1. **Blazor WebAssembly Uygulama** şablonunu seçin.

1. **Gelişmiş** ' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.

1. **Oluştur** ’u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Bir komut kabuğunda, aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code, uygulamanın proje klasörünü açın.

1. Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet** ' i seçin. Visual Studio Code, klasörü otomatik olarak `.vscode` oluşturulan `launch.json` ve dosyaları ekler `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:

1. **Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.

1. Yan çubukta **Web ve konsol**  >  **uygulaması** ' nı seçin.

1. **Blazor WebAssembly Uygulama** şablonunu seçin. **İleri** ’yi seçin.

1. **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın. **Barındırılan ASP.NET Core** onay kutusunu seçin. **İleri** ’yi seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` . **Oluştur** ’u seçin.

   Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.

1. Proje klasörüne gidip projenin çözüm dosyasını () açarak projeyi açın `.sln` .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir komut kabuğunda, aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>SignalRİstemci kitaplığını ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **Çözüm Gezgini** , projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet** ' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .

1. **Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.

1. Arama sonuçlarında, [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) paketi seçin ve ardından **Install** ' ı seçin.

1. **Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam** ' ı seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**Tümleşik terminalde** (araç çubuğundan **Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet** ' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .

1. **Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.

1. Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ve **paket Ekle** ' yi seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Hub ekleme SignalR

`BlazorSignalRApp.Server`Projesinde, bir `Hubs` (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı ( `Hubs/ChatHub.cs` ) ekleyin:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Hub için hizmetler ve uç nokta ekleme SignalR

1. `BlazorSignalRApp.Server`Projede `Startup.cs` dosyasını açın.

1. Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. `Startup.Configure` içinde:

   * İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.
   * Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>RazorSohbet için bileşen kodu ekle

1. `BlazorSignalRApp.Client`Projede `Pages/Index.razor` dosyasını açın.

1. İşaretlemeyi aşağıdaki kodla değiştirin:

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Araç kılavuzunuz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini** , `BlazorSignalRApp.Server` projeyi seçin. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. VS Code, sunucu uygulaması () için bir başlatma profili oluşturmak üzere teklif edildiğinde `.vscode/launch.json` , `program` uygulamanın derlemesini () göstermek için giriş aşağıdakine benzer şekilde görünür `{APPLICATION NAME}.Server.dll` :

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda `BlazorSignalRApp.Server` projeyi seçin. Uygulamayı <kbd>⌘</kbd>hata + <kbd>↩</kbd> <kbd>⌥</kbd> + <kbd>⌘</kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Blazor WebAssemblyBarındırılan uygulama projesi oluşturma
> * SignalRİstemci kitaplığını ekleme
> * Hub ekleme SignalR
> * SignalRHub için hizmetler ve uç nokta ekleme SignalR
> * RazorSohbet için bileşen kodu ekle

Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [IdentitySunucu, WebSockets ve Server-Sent olayları ile taşıyıcı belirteci kimlik doğrulaması](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/introduction>
* [SignalR kimlik doğrulaması için çıkış noktaları arası anlaşma](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
