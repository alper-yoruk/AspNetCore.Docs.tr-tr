---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688528"
---
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC ile çalışmaya başlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.

Uygulama, bir film başlıkları veritabanını yönetir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir modeli ekleyin ve yapı iskelesi yapın.
> * Bir veritabanıyla çalışın.
> * Arama ve doğrulama ekleyin.

Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** Iletişim kutusunda `MvcMovie` **Proje adı**' nı girin. Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.
1. **Oluştur**’u seçin.
1. **Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:
    1. **.NET Core** ve **ASP.NET Core 5,0** açılır.
    1. **ASP.NET Core Web uygulaması (Model-View-Controller)**.
    1. **Oluştur**

![Yeni bir ASP.NET Core Web uygulaması oluşturma ](start-mvc/_static/mvcVS19v16.9.png)

Projeyi oluşturmaya yönelik alternatif yaklaşımlar için bkz. [Visual Studio 'da yeni proje oluşturma](/visualstudio/ide/create-new-project).

Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı. Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var. Bu, temel bir başlatıcı projem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğreticide VS Code anladığı varsayılır. Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**  **Evet**’i seçin

  * `dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.
  * `code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  . Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* **Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:

  * **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.
  * **Hedef çerçeve** seçme seçeneği sunulursa, en son 5. x sürümünü seçin.

  **İleri**’yi seçin.

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır. Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
* Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.
* **Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* **IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz

  ![IIS Express](start-mvc/_static/iis_express.png)

  Aşağıdaki görüntüde uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` . Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

  Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.

  ![Giriş veya dizin sayfası](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin. Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.
* Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.

  Aşağıdaki görüntüde uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.

Uygulama, bir film başlıkları veritabanını yönetir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir modeli ekleyin ve yapı iskelesi yapın.
> * Bir veritabanıyla çalışın.
> * Arama ve doğrulama ekleyin.

Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'da **Yeni proje oluştur**' u seçin.

* **ASP.NET Core Web uygulaması** > **İleri ' yi** seçin.

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin. Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* **Web uygulaması (Model-View-Controller)** seçeneğini belirleyin. Açılan kutularda **.NET Core** ve **ASP.NET Core 3,1**' i seçin ve ardından **Oluştur**' u seçin.

![Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı. Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var. Bu, temel bir başlatıcı projem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğreticide familar, VS Code ile varsayılmıştır. Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**  **Evet**’i seçin

  * `dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.
  * `code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  . Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* **Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:

  * **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.
  * **Hedef çerçeve** seçme seçeneği sunulursa, en son 3. x sürümünü seçin.

  **İleri**’yi seçin.

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır. Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
* Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.
* **Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* **IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz

  ![IIS Express](start-mvc/_static/iis_express.png)

  Aşağıdaki görüntüde uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` . Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

  Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin. Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.
* Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.

  Aşağıdaki görüntüde uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.

Uygulama, bir film başlıkları veritabanını yönetir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir modeli ekleyin ve yapı iskelesi yapın.
> * Bir veritabanıyla çalışın.
> * Arama ve doğrulama ekleyin.

Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'da **Yeni proje oluştur**' u seçin.

* **ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin. Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* **Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.

![Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web ](start-mvc/_static/new_project22-21.png)

Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı. Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var. Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğreticide familar, VS Code ile varsayılmıştır. Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**  **Evet**’i seçin

  * `dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.
  * `code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  . Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.

* **Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:

  * **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.
  * **Hedef çerçeve** seçme seçeneği sunulursa, en son 2. x sürümünü seçin.

  **İleri**’yi seçin.

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır. Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
* Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.
* **Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* **IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz

  ![IIS Express](start-mvc/_static/iis_express.png)

* İzlemeye onay vermek için **Kabul Et**'i seçin. Bu uygulama kişisel bilgileri izlemez. Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` . Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

  Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır. Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.

* İzlemeye onay vermek için **Kabul Et**'i seçin. Bu uygulama kişisel bilgileri izlemez. Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin. Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.
* Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.

* İzlemeye onay vermek için **Kabul Et**'i seçin. Bu uygulama kişisel bilgileri izlemez. Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end
