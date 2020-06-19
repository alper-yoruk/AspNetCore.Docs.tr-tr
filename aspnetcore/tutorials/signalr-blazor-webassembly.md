---
title: SignalR Blazor Webassembly ile ASP.NET Core kullanma
author: guardrex
description: Webassembly ile ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102696"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>SignalR Blazor Webassembly ile ASP.NET Core kullanma

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu öğreticide, SignalR webassembly ile kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir Blazor . Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * BlazorWebassembly barındırılan uygulama projesi oluşturma
> * SignalRİstemci kitaplığını ekleme
> * Hub ekleme SignalR
> * SignalRHub için hizmetler ve uç nokta ekleme SignalR
> * RazorSohbet için bileşen kodu ekle

Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Barındırılan Blazor webassembly uygulama projesi oluşturma

Araç seçiminiz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.

1. Yeni bir proje oluşturma.

1. ** Blazor Uygulama** ' yı seçin ve **İleri ' yi**seçin.

1. **Proje adı** alanına "BlazorSignalRApp" yazın. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**'u seçin.

1. ** Blazor Webassembly uygulama** şablonunu seçin.

1. **Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.

1. **Oluştur**'u seçin.

> [!NOTE]
> Visual Studio 'nun yeni bir sürümünü yükselttiyseniz veya yüklediyseniz ve Blazor webassembly şablonu vs Kullanıcı arabiriminde görünmüyorsa, `dotnet new` daha önce gösterilen komutu kullanarak şablonu yeniden yükleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Bir komut kabuğunda, aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code, uygulamanın proje klasörünü açın.

1. Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin. Visual Studio Code, *launch.js* oluşturulan *. vscode* klasörünü ve dosyaları *üzerindetasks.js* otomatik olarak ekler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:

1. **Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.

1. Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.

1. ** Blazor Webassembly uygulama** şablonunu seçin. **İleri**’yi seçin.

   Aşağıdaki konfigürasyonları onaylayın:

   * **Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.
   * **Kimlik doğrulaması** **yok**olarak ayarlandı.

   **Barındırılan ASP.NET Core** onay kutusunu seçin.

   **İleri**’yi seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` . **Oluştur**'u seçin.

   Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.

1. Proje klasörüne gidip projenin çözüm dosyasını (*. sln*) açarak projeyi açın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir komut kabuğunda, aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>SignalRİstemci kitaplığını ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **Çözüm Gezgini**, **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** *NuGet.org*olarak ayarlandığını doğrulayın.

1. Seçili **Araştır** seçiliyken "Microsoft. aspnetcore.. yazın. SignalR İstemci "ifadesini arayın.

1. Arama sonuçlarında [Microsoft. AspNetCore.. öğesini seçin. SignalR İstemci](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) paketini ve **yüklemeyi**seçin.

1. **Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**Tümleşik terminalde** (araç çubuğundan**Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun *NuGet.org*olarak ayarlandığını doğrulayın.

1. Seçili **Araştır** seçiliyken "Microsoft. aspnetcore.. yazın. SignalR İstemci "ifadesini arayın.

1. Arama sonuçlarında, [Microsoft. AspNetCore ' un yanındaki onay kutusunu SignalR işaretleyin. İstemci](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) paketini ve **paket Ekle**' yi seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Hub ekleme SignalR

**BlazorSignalRApp. Server** projesinde, bir *hub* (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı (*hub/ChatHub. cs*) ekleyin:

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Hub için hizmetler ve uç nokta ekleme SignalR

1. **BlazorSignalRApp. Server** projesinde, *Startup.cs* dosyasını açın.

1. Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. `Startup.Configure` içinde:

   * İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.
   * Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a>RazorSohbet için bileşen kodu ekle

1. **BlazorSignalRApp. Client** projesinde *Pages/Index. Razor* dosyasını açın.

1. İşaretlemeyi aşağıdaki kodla değiştirin:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Araç kılavuzunuz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini**, **BlazorSignalRApp. Server** projesini seçin. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalRBlazorWebassembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. VS Code, sunucu uygulaması (*. vscode/launch.json*) için bir başlatma profili oluşturmak istediğinizde, `program` uygulamanın derlemesine () işaret etmek için giriş aşağıdakine benzer şekilde görünür `{APPLICATION NAME}.Server.dll` :

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalRBlazorWebassembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp. Server** projesini seçin. Uygulamayı <kbd>⌘</kbd>hata + <kbd>↩</kbd> <kbd>⌥</kbd> + <kbd>⌘</kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalRBlazorWebassembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalRBlazorWebassembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * BlazorWebassembly barındırılan uygulama projesi oluşturma
> * SignalRİstemci kitaplığını ekleme
> * Hub ekleme SignalR
> * SignalRHub için hizmetler ve uç nokta ekleme SignalR
> * RazorSohbet için bileşen kodu ekle

Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/introduction>
* [SignalRkimlik doğrulaması için çıkış noktaları arası anlaşma](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
