---
title: SignalR Webassembly Blazor ile ASP.NET Core kullanma
author: guardrex
description: Webassembly ile SignalR Blazor ASP.NET Core kullanan bir sohbet uygulaması oluşturun.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: e86047c9a830cd3ea906d0798f61d90eaee5a18c
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604837"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Blazor WebAssembly ile ASP.NET Core SignalR kullanma

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu öğreticide, Blazor WebAssembly ile SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Blazor WebAssembly barındırılan uygulama projesi oluşturma
> * SignalR istemci kitaplığını ekleme
> * SignalR hub 'ı ekleme
> * SignalR hub 'ı için SignalR Hizmetleri ve uç nokta ekleme
> * Sohbet için Razor bileşeni kodu ekleme

Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Barındırılan Blazor WebAssembly uygulama projesi oluşturma

Visual Studio sürüm 16,6 Preview 2 veya sonraki bir sürümü kullanmadığınız durumlarda, [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) şablonunu ' nı yükledikten sonra. [Microsoft. AspNetCore. components. WebAssembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) paketinin önizleme sürümü vardır, ancak Blazor WebAssembly önizlemededir. Bir komut kabuğunda, aşağıdaki komutu yürütün:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Araç seçiminiz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.

1. **Blazor uygulamasını** seçin ve **İleri ' yi**seçin.

1. **Proje adı** alanına "BlazorSignalRApp" yazın. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**’u seçin.

1. **Blazor WebAssembly uygulama** şablonunu seçin.

1. **Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.

1. **Oluştur**’u seçin.

> [!NOTE]
> Visual Studio 'nun yeni bir sürümünü yükselttiyseniz veya yüklediyseniz ve Blazor WebAssembly şablonu VS Kullanıcı arabiriminde görünmüyorsa, daha önce gösterilen `dotnet new` komutu kullanarak şablonu yeniden yükleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Bir komut kabuğunda, aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code, uygulamanın proje klasörünü açın.

1. Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin. Visual Studio Code, *. vscode* klasörünü oluşturulan *Launch. JSON* ve *Tasks. JSON* dosyaları ile otomatik olarak ekler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Bir komut kabuğunda, aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Mac için Visual Studio, proje klasörüne gidip projenin çözüm dosyasını (*. sln*) açarak projeyi açın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir komut kabuğunda, aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>SignalR istemci kitaplığını ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **Çözüm Gezgini**, **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** *NuGet.org*olarak ayarlandığını doğrulayın.

1. Araştır **seçiliyken,** arama kutusuna "Microsoft. aspnetcore. SignalR. Client" yazın.

1. Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketi seçin ve ardından **Install**' ı seçin.

1. **Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**Tümleşik terminalde** (araç çubuğundan**Görünüm** > **terminali** ) aşağıdaki komutları yürütün:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun *NuGet.org*olarak ayarlandığını doğrulayın.

1. Araştır **seçiliyken,** arama kutusuna "Microsoft. aspnetcore. SignalR. Client" yazın.

1. Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketin yanındaki onay kutusunu Işaretleyin ve **paket Ekle**' yi seçin.

1. **Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>SignalR hub 'ı ekleme

**BlazorSignalRApp. Server** projesinde, bir *hub* (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı (*hub/ChatHub. cs*) ekleyin:

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>SignalR hub 'ı için hizmetler ve uç nokta ekleme

1. **BlazorSignalRApp. Server** projesinde, *Startup.cs* dosyasını açın.

1. `ChatHub` Sınıfın ad alanını dosyanın en üstüne ekleyin:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. SignalR ve yanıt sıkıştırma ara yazılım hizmetlerini şu `Startup.ConfigureServices`şekilde ekleyin:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Denetleyiciler `Startup.Configure` ve istemci tarafı geri dönüş uç noktaları arasında, Hub için bir uç nokta ekleyin:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Sohbet için Razor bileşeni kodu ekleme

1. **BlazorSignalRApp. Client** projesinde *Pages/Index. Razor* dosyasını açın.

1. İşaretlemeyi aşağıdaki kodla değiştirin:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Araç kılavuzunuz için yönergeleri izleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini**, **BlazorSignalRApp. Server** projesini seçin. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. VS Code, sunucu uygulaması (*. vscode/Launch. JSON*) için bir başlatma profili oluşturmak üzere teklif edildiğinde, `program` uygulamanın derlemesini (`{APPLICATION NAME}.Server.dll`) göstermek için aşağıdakine benzer şekilde görünür:

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm** kenar çubuğunda **BlazorSignalRApp. Server** projesini seçin. Uygulamayı hata ayıklamadan çalıştırmak için hata ayıklama veya <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> ile çalıştırmak için <kbd>⌘</kbd>+<kbd>↩</kbd>* * tuşlarına basın.

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. Komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.

1. Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin. Ad ve ileti her iki sayfada da anında görüntülenir:

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Blazor Webassembly barındırılan uygulama projesi oluşturma
> * SignalR İstemci kitaplığını ekleme
> * SignalR Hub ekleme
> * Hub için hizmetler ve uç nokta ekleme SignalR SignalR
> * Sohbet için Razor bileşeni kodu ekleme

Uygulama oluşturma Blazor hakkında daha fazla bilgi edinmek için Blazor belgelere bakın:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/introduction>
