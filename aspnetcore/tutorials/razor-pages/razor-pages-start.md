---
title: 'Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama'
author: rick-anderson
description: Bu öğretici dizisinde ASP.NET Core sayfaların nasıl kullanılacağı gösterilmektedir Razor . Model oluşturmayı, sayfalar için kod oluşturmayı, Razor veri erişimi için Entity Framework Core ve SQL Server kullanmayı, arama işlevselliği eklemeyi, giriş doğrulaması eklemeyi ve geçiş yapmayı kullanarak modeli güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: db05b8243b91a936a6ed72b0445e0770d44df014
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634585"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Öğretici: Razor ASP.NET Core sayfaları kullanmaya başlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Bu, bir serinin ASP.NET Core bir Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Bir Razor Sayfalar Web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>RazorSayfalar Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.
  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)
* Projeyi ** Razor pagesfilmi**olarak adlandırın. Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project * Razor pagesfilmi* olarak adlandırmak önemlidir.
  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  Aşağıdaki Başlatıcı proje oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* `cd`Projeyi içerecek dizine () geçin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Komutu Razor * Razor pagesmovie* klasöründe yeni bir sayfalar projesi oluşturur.
  * `code`Komutu, Visual Studio Code geçerli örneğindeki * Razor pagesmovie* klasörünü açar.

* Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?** **Evet**’i seçin.

  Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

  ![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**. Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.

  ![macOS Web uygulaması şablon seçimi](razor-pages-start/_static/web_app_template_vsmac.png)

* **Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:

  * **Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.
  * **Hedef çerçeve**seçme seçeneği sunulursa, en son 3. x sürümünü seçin.

  **İleri**’yi seçin.

* Projeyi ** Razor pagesfilmi**olarak adlandırın ve **Oluştur**' u seçin.

  ![macOS projeyi Adlandır](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.

### <a name="pages-folder"></a>Sayfalar klasörü

RazorSayfaları ve destekleyici dosyaları içerir. Her Razor sayfa bir dosya çiftidir:

* Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor
* Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.

Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir. Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır. Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>Üzerinde appSettings.js

Bağlantı dizeleri gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Programın giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Uygulama davranışını yapılandıran kodu içerir. Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="next-steps"></a>Sonraki adımlar

Serideki bir sonraki öğreticiye ilerleyin:

> [!div class="step-by-step"]
> [Model ekleme](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Bu, bir serinin ilk öğreticisidir. [Seriler](xref:tutorials/razor-pages/index) , ASP.NET Core sayfaları Web uygulaması oluşturma hakkında temel bilgileri öğretir Razor .

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Bir Razor Sayfalar Web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu öğreticinin sonunda, Razor daha sonraki öğreticilerde oluşturacağınız bir çalışma sayfaları Web uygulamanız olacaktır.

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>RazorSayfalar Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.

* Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* Projeyi ** Razor pagesfilmi**olarak adlandırın. Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project * Razor pagesfilmi* olarak adlandırmak önemlidir.

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  Aşağıdaki Başlatıcı proje oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* `cd`Projeyi içerecek dizine () geçin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Komutu Razor * Razor pagesmovie* klasöründe yeni bir sayfalar projesi oluşturur.
  * `code`Komutu, Visual Studio Code geçerli örneğindeki * Razor pagesmovie* klasörünü açar.

* Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' pagesmovie ' içinde eksik bir iletişim kutusu yok Razor . Bunları ekleyin mi?** **Evet**’i seçin.

  Dosyalar *üzerinde* *launch.js* vetasks.jsiçeren bir *. vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması**' nı seçin  >  **Next**. Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması**  >  **İleri**' yi seçin.

* **Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:

  * **Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.
  * **Hedef çerçeve**seçme seçeneği sunulursa, en son 2. x sürümünü seçin.

  **İleri**’yi seçin.

* Projeyi ** Razor pagesfilmi**olarak adlandırın ve **Oluştur**' u seçin.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır. Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Bunun nedeni, `localhost` Yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

* Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Hata ayıklayıcı olmadan çalıştırmak için **CTRL-F5** tuşlarına basın.

  Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` . Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` . Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

* Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.

  Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001` .

* Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.

### <a name="pages-folder"></a>Sayfalar klasörü

RazorSayfaları ve destekleyici dosyaları içerir. Her Razor sayfa bir dosya çiftidir:

* Sözdizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası. Razor
* Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.

Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir. Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır. Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>Üzerinde appSettings.js

Bağlantı dizeleri gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Programın giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Uygulama davranışını yapılandıran kodu içerir, örneğin, için izin gerektirip gerektirmediğini belirtir cookie . Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Sonraki adımlar

Serideki bir sonraki öğreticiye ilerleyin:

> [!div class="step-by-step"]
> [Model ekleme](xref:tutorials/razor-pages/model)

::: moniker-end
