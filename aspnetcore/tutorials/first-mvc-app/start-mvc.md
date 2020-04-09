---
title: Core MVC ASP.NET ile başlayın
author: rick-anderson
description: Core MVC'ASP.NET nasıl başladığınızı öğrenin.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662479"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Core MVC ASP.NET ile başlayın

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu öğretici, ASP.NET Core MVC web uygulaması oluşturmanın temellerini öğretir.

Uygulama, film başlıklarının bir veritabanını yönetir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir model ekleyin ve iskele.
> * Bir veritabanı ile çalışın.
> * Arama ve doğrulama ekleyin.

Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız var.

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

* Visual Studio'dan **yeni bir proje oluştur'u**seçin.

* **Core Web UygulamasıASP.NET** seçin ve ardından **İleri'yi**seçin.

![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/np_2.1.png)

* Projemin adını **MvcMovie** olarak adlandırın ve **Oluştur'u**seçin. Projemin adını **MvcMovie** olarak adlandırmanız önemlidir, böylece kodu kopyaladiğinizde ad alanı eşleşir.

  ![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/config.png)

* **Web Uygulaması(Model-View-Controller)** seçeneğini belirleyin ve ardından **Oluştur'u**seçin.

![Yeni proje iletişim kutusu, .NET Core sol bölmede, ASP.NET Core web ](start-mvc/_static/new_project30.png)

Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan şablonu kullandı. Şu anda bir proje adı girerek ve birkaç seçenek seçerek çalışan bir uygulamanız var. Bu temel bir başlangıç projesidir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğretici VS Kodu ile familarity varsayar. Daha fazla bilgi için VS Kodu ve [Visual Studio Code ile](#visual-studio-code-help) [başlarken](https://code.visualstudio.com/docs) yardım bakın.

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'MvcMovie'den eksiktir. Onları eklemek mi?**  **Evet'i** Seçin

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* klasöründe yeni bir ASP.NET Core MVC projesi oluşturur.
  * `code -r MvcMovie`: Visual Studio Code'da *MvcMovie.csproj* proje dosyasını yükler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

  ![macOS Yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* **İleride** **.NET Core** > **App** > Web Uygulamasını **(Model-View-Controller) seçin.** >

  ![macOS Yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* Yeni **ASP.NET Core Web API iletişim günlüğünüze yapılandırıldığında** **,.NET Core 3.1'in** **Hedef Çerçevesini** ayarlayın.

  ![macOS .NET Core 3.1 seçimi](./start-mvc/_static/new_project_31_vsmac.png)

* Projeyi **MvcMovie**olarak adlandırın ve ardından **Oluştur'u**seçin.

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı hata ayıklama modunda çalıştırmak için **Ctrl-F5'i** seçin.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın. Adres çubuğunun gösterdiğine `localhost:port#` dikkat `example.com`edin, gibi bir şey değil. Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
* Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar. Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.
* Uygulamayı **Hata** Ayıklama menü öğesinden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz:

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* **IIS Express** düğmesini seçerek uygulamayı hata ayıklayabilirsiniz

  ![IIS Ekspresi](start-mvc/_static/iis_express.png)

  Aşağıdaki resim uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `https://localhost:5001`başlattı ve gezinir . Adres çubuğu `localhost:port:5001` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.

  Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar. Birçok geliştirici sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama modunu kullanmayı tercih ediyor.

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı başlatmak için > **Hata Ayıklama olmadan** **Çalıştır'ı**seçin. Mac için Visual Studio [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusubaşlatılır, bir `http://localhost:port`tarayıcı başlatır ve *bağlantı noktasının* rasgele seçilmiş bir bağlantı noktası numarası olduğu yere doğru yol alar.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda farklı bir bağlantı noktası numarası görürsünüz.
* Uygulamayı **Çalıştır** menüsünden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz.

  Aşağıdaki resim uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinin ve bazı kod yazmaya başlarsınız.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Bu öğretici, ASP.NET Core MVC web uygulaması oluşturmanın temellerini öğretir.

Uygulama, film başlıklarının bir veritabanını yönetir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması oluşturun.
> * Bir model ekleyin ve iskele.
> * Bir veritabanı ile çalışın.
> * Arama ve doğrulama ekleyin.

Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız var.

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

* Visual Studio'dan **yeni bir proje oluştur'u**seçin.

* **Core Web UygulamasıASP.NET** seçin ve ardından **İleri'yi**seçin.

![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/np_2.1.png)

* Projemin adını **MvcMovie** olarak adlandırın ve **Oluştur'u**seçin. Projemin adını **MvcMovie** olarak adlandırmanız önemlidir, böylece kodu kopyaladiğinizde ad alanı eşleşir.

  ![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/config.png)


* **Web Uygulaması(Model-View-Controller)** seçeneğini belirleyin ve ardından **Oluştur'u**seçin.

![Yeni proje iletişim kutusu, .NET Core sol bölmede, ASP.NET Core web ](start-mvc/_static/new_project22-21.png)

Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan şablonu kullandı. Şu anda bir proje adı girerek ve birkaç seçenek seçerek çalışan bir uygulamanız var. Bu temel bir başlangıç projesi, ve başlamak için iyi bir yer.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğretici VS Kodu ile familarity varsayar. Daha fazla bilgi için VS Kodu ve [Visual Studio Code ile](#visual-studio-code-help) [başlarken](https://code.visualstudio.com/docs) yardım bakın.

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.
* Şu komutu çalıştırın:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'MvcMovie'den eksiktir. Onları eklemek mi?**  **Evet'i** Seçin

  * `dotnet new mvc -o MvcMovie`: *MvcMovie* klasöründe yeni bir ASP.NET Core MVC projesi oluşturur.
  * `code -r MvcMovie`: Visual Studio Code'da *MvcMovie.csproj* proje dosyasını yükler.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

  ![macOS Yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* **İleride** **.NET Core** > **App** > Web Uygulamasını **(Model-View-Controller) seçin.** >

  ![macOS Yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* Yeni **ASP.NET Core Web API iletişim günlüğünüzü yapılandırın,** **.NET Core 2.2'nin**varsayılan **Hedef Çerçevesini** kabul edin.

  ![macOS .NET Core 2.2 seçimi](./start-mvc/_static/new_project_22_vsmac.png)

* Projeyi **MvcMovie**olarak adlandırın ve ardından **Oluştur'u**seçin.

---

### <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uygulamayı hata ayıklama modunda çalıştırmak için **Ctrl-F5'i** seçin.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın. Adres çubuğunun gösterdiğine `localhost:port#` dikkat `example.com`edin, gibi bir şey değil. Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.
* Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar. Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.
* Uygulamayı **Hata** Ayıklama menü öğesinden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz:

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* **IIS Express** düğmesini seçerek uygulamayı hata ayıklayabilirsiniz

  ![IIS Ekspresi](start-mvc/_static/iis_express.png)

* İzlemeye onay vermek için **Kabul et'i** seçin. Bu uygulama kişisel bilgileri izlemez. Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/privacy.png)

  Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `https://localhost:5001`başlattı ve gezinir . Adres çubuğu `localhost:port:5001` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.

  Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar. Birçok geliştirici sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama modunu kullanmayı tercih ediyor.

* İzlemeye onay vermek için **Kabul et'i** seçin. Bu uygulama kişisel bilgileri izlemez. Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/privacy.png)

  Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Uygulamayı başlatmak için > **Hata Ayıklama olmadan** **Çalıştır'ı**seçin. Mac için Visual Studio [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusubaşlatılır, bir `http://localhost:port`tarayıcı başlatır ve *bağlantı noktasının* rasgele seçilmiş bir bağlantı noktası numarası olduğu yere doğru yol alar.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır. Uygulamayı çalıştırdığınızda farklı bir bağlantı noktası numarası görürsünüz.
* Uygulamayı **Çalıştır** menüsünden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz.

* İzlemeye onay vermek için **Kabul et'i** seçin. Bu uygulama kişisel bilgileri izlemez. Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinin ve bazı kod yazmaya başlarsınız.

> [!div class="step-by-step"]
> [Sonraki](adding-controller.md)

::: moniker-end
