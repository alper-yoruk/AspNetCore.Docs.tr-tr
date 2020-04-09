---
title: 'Öğretici: ASP.NET Core Razor Pages ile başlayın'
author: rick-anderson
description: Öğreticiler Bu dizi nasıl ASP.NET Core Razor Pages nasıl kullanılacağını gösterir. Nasıl bir model oluşturacağınızı, Razor sayfaları için kod oluşturmayı, veri erişimi için Entity Framework Core ve SQL Server'ı kullanmayı, arama işlevselliği eklemeyi, giriş doğrulamayı eklemeyi ve modeli güncelleştirmek için geçişleri nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658545"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Öğretici: ASP.NET Core Razor Pages ile başlayın

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Bu bir ASP.NET Core Razor Pages web uygulaması oluşturma temellerini öğretir bir serinin ilk öğretici.

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, film veritabanını yöneten bir uygulamanız olur.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Razor Pages web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu eğitimin sonunda, daha sonraki eğitimlerde üzerine oluşturacağınız çalışan bir Razor Pages web uygulamasına sahip olacaksınız.

![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor Sayfaları web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* Yeni bir ASP.NET Core Web Uygulaması oluşturun ve **İleri'yi**seçin.
  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2.1.png)
* Projeye **RazorPagesMovie**adını verebedin. *ProjerazorPagesMovie* adını önemlidir, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.
  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/config.png)

* Açılır ASP.NET **Core 3.1'i** seçin, **Web Uygulaması**ve ardından **Oluştur'u**seçin.

![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/3/npx.png)

  Aşağıdaki başlangıç projesi oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* Projeyi içerecek dizine`cd`() değiştirin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Komut, `dotnet new` *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.
  * Komut, `code` Visual Studio Code'un geçerli örneğinde *RazorPagesMovie* klasörünü açar.

* Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve 'RazorPagesMovie'den hata ayıklama eksiktir. Onları eklemek mi?** **Evet'i**seçin.

  *Launch.json* ve *tasks.json* dosyalarını içeren *bir .vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

![macOS Yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **Gelecek** **.NET Core** > **App** > **Web Uygulamasını** > Seçin.

  ![macOS Yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* Yeni Web Uygulaması iletişim **günlüğünüze Yapıla,** **Hedef Çerçeveyi** **.NET Core 3.1**olarak ayarlayın.

  ![macOS .NET Core 3.1 seçimi](razor-pages-start/_static/targetframework3.png)

* Projeye **RazorPagesMovie**adını ver ve ardından **Oluştur'u**seçin.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

Aşağıda, daha sonraki eğitimlerde çalışacağınız ana proje klasörleri ve dosyalarına genel bir bakış verebilirsiniz.

### <a name="pages-folder"></a>Sayfalar klasörü

Razor sayfaları ve destekleyici dosyalar içerir. Her Razor sayfası bir dosya çiftidir:

* Razor sözdizimini kullanarak C# koduyla HTML biçimlendirmesi içeren *bir .cshtml* dosyası.
* Sayfa olaylarını işleyen C# kodu içeren *.cshtml.cs* dosyası.

Destekleyen dosyaların alt altı yla başlayan adları vardır. Örneğin, *_Layout.cshtml* dosyası tüm sayfalarda yaygın olan UI öğelerini yapılandırır. Bu dosya, sayfanın üst kısmındaki gezinti menüsünü ve sayfanın altındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyalar içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Bağlantı dizeleri gibi yapılandırma verileri içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Programın giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Uygulama davranışını yapılandıran kod içerir. Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="next-steps"></a>Sonraki adımlar

Serinin bir sonraki öğretici için advance:

> [!div class="step-by-step"]
> [Model ekleme](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Bu bir serinin ilk öğretici. [Dizi](xref:tutorials/razor-pages/index) bir ASP.NET Core Razor Pages web uygulaması oluşturma temellerini öğretir.

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, film veritabanını yöneten bir uygulamanız olur.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Razor Pages web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu eğitimin sonunda, daha sonraki eğitimlerde üzerine oluşturacağınız çalışan bir Razor Pages web uygulamasına sahip olacaksınız.

![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor Sayfaları web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.

* Yeni bir ASP.NET Core Web Uygulaması oluşturun ve **İleri'yi**seçin.

  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2.1.png)

* Projeye **RazorPagesMovie**adını verebedin. *ProjerazorPagesMovie* adını önemlidir, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.

  ![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/config.png)

* Açılır ASP.NET **Core 2.2'yi** seçin, **Web Uygulaması**ve ardından **Oluştur'u**seçin.

![yeni ASP.NET Çekirdek Web Uygulaması](razor-pages-start/_static/np_2_2.2.png)

  Aşağıdaki başlangıç projesi oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* Projeyi içerecek dizine`cd`() değiştirin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Komut, `dotnet new` *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.
  * Komut, `code` Visual Studio Code'un geçerli örneğinde *RazorPagesMovie* klasörünü açar.

* Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve 'RazorPagesMovie'den hata ayıklama eksiktir. Onları eklemek mi?** **Evet'i**seçin.

  *Launch.json* ve *tasks.json* dosyalarını içeren *bir .vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **Yeni Çözüm**seçin.

![macOS Yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **Gelecek** **.NET Core** > **App** > **Web Uygulamasını** > Seçin.

  ![macOS Yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* Yeni **ASP.NET Core Web API iletişim günlüğünüzde** **Hedef Çerçeveyi** **.NET Core 3.1**olarak ayarlayın.

  ![macOS .NET Core 3.0 seçimi](razor-pages-start/_static/targetframework3.png)

* Projeye **RazorPagesMovie**adını ver ve ardından **Oluştur'u**seçin.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın. Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

* Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Hata ayıklama olmadan çalıştırmak için **Ctrl-F5** tuşuna basın.

  Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `http://localhost:5001`başlattı ve gezinir . Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey . Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır. Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.

* Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Hata ayıklama olmadan çalıştırmak için **Cmd-Opt-F5** tuşuna basın.

  Visual Studio [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı başlattı `http://localhost:5001`ve gezinir .

* Uygulamanın ana sayfasında, izlemeyi kabul etmek için **Kabul** et'i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa [Birliği'nin Genel Veri Koruma Yönetmeliği'ne (GDPR)](xref:security/gdpr)uymanız gerektiğinde onay özelliğini içerir.

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Aşağıdaki resim, izleme izni verdikten sonra uygulamayı gösterir:

  ![Ana Sayfa veya Dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

Aşağıda, daha sonraki eğitimlerde çalışacağınız ana proje klasörleri ve dosyalarına genel bir bakış verebilirsiniz.

### <a name="pages-folder"></a>Sayfalar klasörü

Razor sayfaları ve destekleyici dosyalar içerir. Her Razor sayfası bir dosya çiftidir:

* Razor sözdizimini kullanarak C# koduyla HTML biçimlendirmesi içeren *bir .cshtml* dosyası.
* Sayfa olaylarını işleyen C# kodu içeren *.cshtml.cs* dosyası.

Destekleyen dosyaların alt altı yla başlayan adları vardır. Örneğin, *_Layout.cshtml* dosyası tüm sayfalarda yaygın olan UI öğelerini yapılandırır. Bu dosya, sayfanın üst kısmındaki gezinti menüsünü ve sayfanın altındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyalar içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Bağlantı dizeleri gibi yapılandırma verileri içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Programın giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Çerezler için onay gerektiremesi gibi uygulama davranışını yapılandıran kod içerir. Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğretici Youtube sürümü](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Sonraki adımlar

Serinin bir sonraki öğretici için advance:

> [!div class="step-by-step"]
> [Model ekleme](xref:tutorials/razor-pages/model)

::: moniker-end
