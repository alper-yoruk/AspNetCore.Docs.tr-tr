---
title: 'Öğretici: ASP.NET Core Razor sayfaları kullanmaya başlama'
author: rick-anderson
description: Bu öğretici dizisinde ASP.NET Core sayfaların nasıl kullanılacağı Razor gösterilmektedir. Model oluşturmayı, sayfalar için Razor kod oluşturmayı, veri erişimi için Entity Framework Core ve SQL Server kullanmayı, arama işlevselliği eklemeyi, giriş doğrulaması eklemeyi ve geçiş yapmayı kullanarak modeli güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774879"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Öğretici: ASP.NET Core Razor Pages ile çalışmaya başlama

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Bu, bir serinin ASP.NET Core Razor Pages Web uygulaması oluşturma hakkında temel bilgileri öğretir.

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Razor Pages bir Web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu öğreticinin sonunda, daha sonraki öğreticilerde oluşturacağınız çalışan bir Razor Pages Web uygulamasına sahipsiniz.

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor Sayfaları web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.
  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)
* Projeyi **RazorPagesMovie**olarak adlandırın. Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.
  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* Açılan **Web uygulamasındaki** **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur**' u seçin.

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/3/npx.png)

  Aşağıdaki Başlatıcı proje oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* Projeyi içerecek dizine (`cd`) geçin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` Komut *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.
  * Komut, Visual Studio Code geçerli örneğindeki RazorPagesMovie klasörünü açar. *RazorPagesMovie* `code`

* Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?** **Evet**' i seçin.

  *Launch. JSON* ve *Tasks. JSON* dosyalarını içeren bir *. vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **Daha sonra** **.NET Core** > **App** > **Web uygulaması** > ' nı seçin.

  ![macOS yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* **Yeni Web uygulamanızı yapılandırın** Iletişim kutusunda **hedef Framework 'ü** **.NET Core 3,1**olarak ayarlayın.

  ![macOS .NET Core 3,1 seçimi](razor-pages-start/_static/targetframework3.png)

* Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

Aşağıda, daha sonraki öğreticilerde birlikte çalışacağımız ana proje klasörlerine ve dosyalarına genel bir bakış sunulmaktadır.

### <a name="pages-folder"></a>Sayfalar klasörü

Razor sayfaları ve destekleyici dosyalar içerir. Her Razor sayfası bir dosya çiftidir:

* Razor söz dizimi kullanarak C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası.
* Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.

Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir. Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır. Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings. JSON

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

Bu, bir serinin ilk öğreticisidir. [Seriler](xref:tutorials/razor-pages/index) , bir ASP.NET Core Razor pages Web uygulaması oluşturma hakkında temel bilgileri öğretir.

[!INCLUDE[](~/includes/advancedRP.md)]

Serinin sonunda, bir film veritabanını yöneten bir uygulamanız olacaktır.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Razor Pages bir Web uygulaması oluşturun.
> * Uygulamayı çalıştırın.
> * Proje dosyalarını inceleyin.

Bu öğreticinin sonunda, daha sonraki öğreticilerde oluşturacağınız çalışan bir Razor Pages Web uygulamasına sahipsiniz.

![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor Sayfaları web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.

* Yeni bir ASP.NET Core Web uygulaması oluşturun ve **İleri ' yi**seçin.

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2.1.png)

* Projeyi **RazorPagesMovie**olarak adlandırın. Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için Project *RazorPagesMovie* olarak adı vermek önemlidir.

  ![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/config.png)

* Açılan **Web uygulamasındaki** **ASP.NET Core 2,2** ' i seçin ve ardından **Oluştur**' u seçin.

![Yeni ASP.NET Core Web uygulaması](razor-pages-start/_static/np_2_2.2.png)

  Aşağıdaki Başlatıcı proje oluşturulur:

  ![Çözüm Gezgini](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.

* Projeyi içerecek dizine (`cd`) geçin.

* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` Komut *RazorPagesMovie* klasöründe yeni bir Razor Pages projesi oluşturur.
  * Komut, Visual Studio Code geçerli örneğindeki RazorPagesMovie klasörünü açar. *RazorPagesMovie* `code`

* Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' RazorPagesMovie ' içinde eksik olduğunu soran bir iletişim kutusu yok. Bunları ekleyin mi?** **Evet**' i seçin.

  *Launch. JSON* ve *Tasks. JSON* dosyalarını içeren bir *. vscode* dizini, projenin kök dizinine eklenir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Dosya** > **yeni çözüm**' ü seçin.

![macOS yeni çözüm](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **Daha sonra** **.NET Core** > **App** > **Web uygulaması** > ' nı seçin.

  ![macOS yeni proje iletişim kutusu](razor-pages-start/_static/webapp.png)

* **Yeni ASP.NET Core Web API 'Nizi yapılandırın** Iletişim kutusunda **hedef Framework 'ü** **.NET Core 3,1**olarak ayarlayın.

  ![macOS .NET Core 3,0 seçimi](razor-pages-start/_static/targetframework3.png)

* Projeyi **RazorPagesMovie**olarak adlandırın ve **Oluştur**' u seçin.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır. Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir. Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir. Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.

* Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Hata ayıklayıcı olmadan çalıştırmak için **CTRL-F5** tuşlarına basın.

  Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001`. Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir. Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır. Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.

* Uygulamanın giriş sayfasında, izlemeye izin vermek için **kabul et** ' i seçin.

  Bu uygulama kişisel bilgileri izlemez, ancak proje şablonu, Avrupa Birliği 'nin [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)ile uyumlu olması için ihtiyaç duymanız durumunda izin özelliğini içerir.

  ![Giriş veya dizin sayfası](razor-pages-start/_static/homeGDPR2.2.png)

  Aşağıdaki görüntüde, izlemeye onay verdikten sonra uygulama gösterilmektedir:

  ![Giriş veya dizin sayfası](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Hata ayıklayıcı olmadan çalıştırmak için **cmd-opt-F5** tuşuna basın.

  Visual Studio, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `http://localhost:5001`.

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

Sayfaları Razor ve destekleyici dosyaları içerir. Her Razor sayfa bir dosya çiftidir:

* Sözdizimi kullanarak Razor C# kodu ile HTML işaretlemesi içeren bir *. cshtml* dosyası.
* Sayfa olaylarını işleyen C# kodu içeren bir *. cshtml.cs* dosyası.

Destekleyici dosyalar bir alt çizgiyle başlayan adlara sahiptir. Örneğin, *_Layout. cshtml* dosyası tüm sayfalarda ortak kullanıcı arabirimi öğelerini yapılandırır. Bu dosya sayfanın en üstündeki gezinti menüsünü ve sayfanın alt kısmındaki telif hakkı bildirimini ayarlar. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Wwwroot klasörü

HTML dosyaları, JavaScript dosyaları ve CSS dosyaları gibi statik dosyaları içerir. Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings. JSON

Bağlantı dizeleri gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Programın giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Tanımlama bilgilerinin onayını gerektirip gerektirmediğini belirten uygulama davranışını yapılandıran kodu içerir. Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Sonraki adımlar

Serideki bir sonraki öğreticiye ilerleyin:

> [!div class="step-by-step"]
> [Model ekleme](xref:tutorials/razor-pages/model)

::: moniker-end
