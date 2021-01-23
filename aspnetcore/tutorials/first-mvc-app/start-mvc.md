---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710794"
---
# <a name="get-started-with-aspnet-core-mvc"></a>ASP.NET Core MVC ile çalışmaya başlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu, denetleyiciler ve görünümlerle ASP.NET Core MVC web geliştirmesini öğretir bir serinin ilk öğreticisidir.

Serinin sonunda, film verilerini yöneten ve görüntüleyen bir uygulamanız olacaktır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir modeli ekleyin ve yapı iskelesi yapın.
> * Bir veritabanıyla çalışın.
> * Arama ve doğrulama ekleyin.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

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

* Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
* **Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.
* **Yeni projenizi yapılandırın** Iletişim kutusunda `MvcMovie` **Proje adı**' nı girin. *Mvcfilmi* adlı projeyi adlandırmak önemlidir. Kod kopyalanırken, büyük küçük harf her `namespace` eşleştirmelere eşleşmelidir.
* **Oluştur**’u seçin.
* **Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:
  * **.NET Core** ve **ASP.NET Core 5,0** açılır.
  * **ASP.NET Core Web uygulaması (Model-View-Controller)**.
  * **Create**.

![Yeni bir ASP.NET Core Web uygulaması oluşturma ](start-mvc/_static/mvcVS19v16.9.png)

Projeyi oluşturmaya yönelik alternatif yaklaşımlar için bkz. [Visual Studio 'da yeni proje oluşturma](/visualstudio/ide/create-new-project).

Visual Studio, oluşturulan MVC projesi için varsayılan proje şablonunu kullandı. Oluşturulan proje:

* , Çalışan bir uygulamadır.
* Temel bir başlatıcı projem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğreticide VS Code anladığı varsayılır. Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help).

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* `cd`Projeyi içerecek dizine () geçin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Gerekli varlıklar içeren bir iletişim kutusu görüntülenirse **ve hata ayıklama ' MvcMovie ' içinde yok. Bunlara eklensin mi?**, **Evet** ' i seçin

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
  * **İleri**’yi seçin.

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Uygulamayı hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 ' i seçin.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)başlatır.
  * Uygulamayı çalıştırır.

  Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:

* Kod değişiklikleri yapın.
* Dosyayı kaydedin.
* Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.

**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:

![Hata ayıklama menüsü](start-mvc/_static/debug_menu50.png)

**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz

![IIS Express](start-mvc/_static/iis_express50.png)

Aşağıdaki görüntüde uygulama gösterilmektedir:

![Giriş veya dizin sayfası](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 ' i seçin.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * [Kestrel](xref:fundamentals/servers/kestrel) başlatır
  * Bir tarayıcı başlatır.
  * Şuraya gider `https://localhost:5001` .

  Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:

* Kod değişiklikleri yapın.
* Dosyayı kaydedin.
* Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.

  ![Giriş veya dizin sayfası](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*  > Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.

  Mac için Visual Studio:

  * [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusunu başlatır.
  * Bir tarayıcı başlatır.
  * `http://localhost:port`, *Bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarası olduğunda şuraya gider.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.

Aşağıdaki görüntüde uygulama gösterilmektedir:

![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.

> [!div class="step-by-step"]
> [Sonraki: denetleyici ekleme](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu, denetleyiciler ve görünümlerle ASP.NET Core MVC web geliştirmesini öğretir bir serinin ilk öğreticisidir.

Serinin sonunda, film verilerini yöneten ve görüntüleyen bir uygulamanız olacaktır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir modeli ekleyin ve yapı iskelesi yapın.
> * Bir veritabanıyla çalışın.
> * Arama ve doğrulama ekleyin.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

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

  ![Yeni bir ASP.NET Core Web uygulaması projesi oluşturma](start-mvc/_static/np_2.1.png)

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin. Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.

  ![Yeni projenizi yapılandırın](start-mvc/_static/config.png)

* **Web uygulaması (Model-View-Controller)** seçeneğini belirleyin. Açılan kutularda **.NET Core** ve **ASP.NET Core 3,1**' i seçin ve ardından **Oluştur**' u seçin.

  ![Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Visual Studio, oluşturulan MVC projesi için varsayılan proje şablonunu kullandı. Oluşturulan proje:

* , Çalışan bir uygulamadır.
* Temel bir başlatıcı projem.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğreticide VS Code anladığı varsayılır. Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help).

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunlara eklensin mi?**, **Evet**' i seçin.

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
  * **İleri**’yi seçin.

* Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Uygulamayı hata ayıklamadan çalıştırmak için CTRL + F5 ' i seçin.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)başlatır.
  * Uygulamayı çalıştırır.

  Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:

* Kod değişiklikleri yapın.
* Dosyayı kaydedin.
* Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.

**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:

![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz

![IIS Express](start-mvc/_static/iis_express.png)

Aşağıdaki görüntüde uygulama gösterilmektedir:

![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uygulamayı hata ayıklamadan çalıştırmak için CTRL + F5 ' i seçin.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * [Kestrel](xref:fundamentals/servers/kestrel) başlatır
  * Bir tarayıcı başlatır.
  * Şuraya gider `https://localhost:5001` .

  Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

CTRL + F5 ' i seçerek uygulamayı hata ayıklamadan başlatmak şunları yapmanıza olanak sağlar:

* Kod değişiklikleri yapın.
* Dosyayı kaydedin.
* Tarayıcıyı hızlıca yenileyin ve kod değişikliklerini görüntüleyin.

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*  > Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.

  Mac için Visual Studio: [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusunu başlatır, bir tarayıcı başlatır ve ' a gider; `http://localhost:port` burada *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.

[!INCLUDE[](~/includes/trustCertMac.md)]

Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Yerel bilgisayarınız için Standart ana bilgisayar adı `localhost` . Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.

Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.

Aşağıdaki görüntüde uygulama gösterilmektedir:

![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end
