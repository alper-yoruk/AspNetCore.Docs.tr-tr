---
title: ASP.NET Core ile başlayınSignalR
author: bradygaster
description: Bu öğreticide, Core'ASP.NET SignalRkullanan bir sohbet uygulaması oluşturursunuz.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994616"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Öğretici: ASP.NET Core SignalR ile başlayın

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temellerini öğretir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir web projesi oluşturun.
> * SignalR istemci kitaplığını ekleyin.
> * Bir SignalR hub'ı oluşturun.
> * Projeyi SignalR kullanacak şekilde yapılandırın.
> * Bağlı tüm istemcilere herhangi bir istemciden ileti gönderen kod ekleyin.

Sonunda, çalışan bir sohbet uygulamanız olur:

![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Bir web uygulaması projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Menüden Dosya **> Yeni Proje'yi**seçin.

* Yeni **bir proje oluştur** iletişim **kutusunda, Çekirdek Web UygulamasıASP.NET'nu**seçin ve ardından **İleri'yi**seçin.

* Yeni proje iletişim **günlüğüne Yapıla,** project *SignalRChat'i*adlandırın ve ardından **Oluştur'u**seçin.

* Yeni **bir ASP.NET Core web Uygulaması oluştur** iletişim kutusunda **.NET Core** ve ASP.NET Core **3.0'ı**seçin. 

* Razor Pages kullanan bir proje oluşturmak için **Web Uygulaması'nı** seçin ve ardından **Oluştur'u**seçin.

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal) yeni proje klasöründeki klasöre açın.

* Aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Menüden Dosya **> Yeni Çözüm'ünü**seçin.

* **.NET Core > App > Web Application** **(Web Uygulaması (Model-View-Controller)** seçeneğini seçmeyin) seçeneğini belirleyin ve sonra **İleri'yi**seçin.

* **Hedef Çerçevenin** **.NET Core 3.0**olarak ayarlandığından emin olun ve sonra **İleri'yi**seçin.

* Projeye *SignalRChat*adını ver ve ardından **Oluştur'u**seçin.

---

## <a name="add-the-signalr-client-library"></a>SignalR istemci kitaplığını ekleme

SignalR sunucu kitaplığı, Core 3.0 paylaşılan ASP.NET çerçevesine dahildir. JavaScript istemci kitaplığı projeye otomatik olarak dahil edilemiyor. Bu öğretici için, istemci kitaplığını *unpkg'dan*almak için Kitaplık Yöneticisi'ni (LibMan) kullanırsınız. unpkg npm, Node.js paket yöneticisi bulunan bir şey teslim edebilirsiniz bir içerik dağıtım ağı (CDN) olduğunu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* **Çözüm Gezgini'nde,** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin.

* **İstemci Tarafı Kitaplığı Ekle** iletişim kutusunda, **Sağlayıcı** **unpkg'ı**seçin.

* **Kitaplık**için `@microsoft/signalr@latest`, girin .

* **Belirli dosyaları seçin,** *dist/tarayıcı* klasörünü genişletin ve *signalr.js* ve *signalr.min.js'i*seçin.

* **Hedef Konumu** *wwwroot/js/signalr/* olarak ayarlayın ve **Yükle'yi**seçin.

  ![İstemci-Side Kitaplığı ekle iletişim kutusu - kitaplık seçin](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan bir *wwwroot/js/signalr* klasörü oluşturur ve seçilen dosyaları kopyalar.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Tümleşik terminalde, LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın. Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametreler aşağıdaki seçenekleri belirtir:
  * Unpkg sağlayıcısını kullanın.
  * Dosyaları *wwwroot/js/signalr* hedefine kopyalayın.
  * Yalnızca belirtilen dosyaları kopyalayın.

  Çıktı aşağıdaki örnek gibi görünür:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Terminalde,** LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Proje klasörüne *(SignalRChat.csproj* dosyasını içeren dosya) gidin.

* LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametreler aşağıdaki seçenekleri belirtir:
  * Unpkg sağlayıcısını kullanın.
  * Dosyaları *wwwroot/js/signalr* hedefine kopyalayın.
  * Yalnızca belirtilen dosyaları kopyalayın.

  Çıktı aşağıdaki örnek gibi görünür:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>SignalR hub'ı oluşturma

*Hub,* istemci-sunucu iletişimini işleyen üst düzey bir ardışık hatlar olarak hizmet veren bir sınıftır.

* SignalRChat proje klasöründe bir *Hubs* klasörü oluşturun.

* *Hub'lar* klasöründe, aşağıdaki koda sahip *ChatHub.cs* bir dosya oluşturun:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Sınıf `ChatHub` SinyalR `Hub` sınıfından devralır. Sınıf `Hub` bağlantıları, grupları ve iletileri yönetir.

  Yöntem, `SendMessage` tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir. Yöntemi çağıran JavaScript istemci kodu daha sonra öğreticide gösterilir. SignalR kodu maksimum ölçeklenebilirlik sağlamak için asynchronous olduğunu.

## <a name="configure-signalr"></a>SignalR'i yapılandır

SignalR sunucusu SignalR isteklerini SignalR'a iletecek şekilde yapılandırılmalıdır.

* *Startup.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Bu değişiklikler, ASP.NET Çekirdek bağımlılık enjeksiyon ve yönlendirme sistemlerine SignalR ekler.

## <a name="add-signalr-client-code"></a>SignalR istemci kodu ekle

* *Pages\Index.cshtml'deki* içeriği aşağıdaki kodla değiştirin:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Yukarıdaki kod:

  * Ad ve ileti metni için metin kutuları ve gönder düğmesi oluşturur.
  * SignalR hub'ından alınan iletileri görüntülemek `id="messagesList"` için bir liste oluşturur.
  * SignalR'a ve bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna komut dosyası başvurularını içerir.

* *wwwroot/js* klasöründe, aşağıdaki kodu içeren bir *chat.js* dosyası oluşturun:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Yukarıdaki kod:

  * Bir bağlantı oluşturur ve başlatır.
  * Gönder düğmesine, hub'a ileti gönderen bir işleyici ekler.
  * Bağlantı nesnesine hub'dan ileti alan ve bunları listeye ekleyen bir işleyici ekler.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Hata ayıklama olmadan uygulamayı çalıştırmak için **CTRL+F5** tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Tümleşik terminalde aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Menüden, **Hata Ayıklama olmadan Çalıştır > Başlat'ı**seçin.

---

* URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

* Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **İleti Gönder** düğmesini seçin.

  Ad ve ileti her iki sayfada anında görüntülenir.

  ![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Uygulama çalışmıyorsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin. HTML ve JavaScript kodunuzla ilgili hatalar görebilirsiniz. Örneğin, *signalr.js'yi* yönlendirilmiş ten farklı bir klasöre koyduğunuzu varsayalım. Bu durumda, bu dosyaya yapılan başvuru çalışmaz ve konsolda bir 404 hatası görürsünüz.
>   ![signalr.js hata bulunamadı](signalr/_static/3.x/f12-console.png)
> * Chrome'da ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY hata alırsanız, geliştirme sertifikanızı güncelleştirmek için aşağıdaki komutları çalıştırın:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğretici, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temellerini öğretir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:   

> [!div class="checklist"]  
> * Bir web projesi oluşturun.   
> * SignalR istemci kitaplığını ekleyin. 
> * Bir SignalR hub'ı oluşturun.   
> * Projeyi SignalR kullanacak şekilde yapılandırın.   
> * Bağlı tüm istemcilere herhangi bir istemciden ileti gönderen kod ekleyin.  
Sonunda, çalışan bir sohbet uygulaması olacak: ![SignalR örnek uygulaması](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Ön koşullar    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Web projesi oluşturma 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Menüden Dosya **> Yeni Proje'yi**seçin. 

* Yeni **Proje** iletişim kutusunda, **Web > ASP.NET Core Web Uygulaması > Yüklü > Visual C# ASP.NET'yi**seçin. Proje *SignalRChat*adı . 

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Razor Pages kullanan bir proje oluşturmak için **Web Uygulaması'nı** seçin. 

* **.NET Core'un**hedef çerçevesini seçin, **Core 2.2ASP.NET**seçin ve **Tamam'ı**tıklatın.    

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal) yeni proje klasöründeki klasöre açın.  

* Aşağıdaki komutları çalıştırın:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)   

* Menüden Dosya **> Yeni Çözüm'ünü**seçin.    

* **ASP.NET Core Web App > .NET Core > App'ı** seçin **(ASP.NET Çekirdek Web Uygulaması (MVC)** seçmeyin).  

* **Sonraki'ni**seçin.  

* Projeye *SignalRChat*adını ver ve ardından **Oluştur'u**seçin.   

--- 

## <a name="add-the-signalr-client-library"></a>SignalR istemci kitaplığını ekleme   

SignalR sunucu kitaplığı meta `Microsoft.AspNetCore.App` pakete dahildir. JavaScript istemci kitaplığı projeye otomatik olarak dahil edilemiyor. Bu öğretici için, istemci kitaplığını *unpkg'dan*almak için Kitaplık Yöneticisi'ni (LibMan) kullanırsınız. unpkg npm, Node.js paket yöneticisi bulunan bir şey teslim edebilirsiniz bir içerik dağıtım ağı (CDN) olduğunu. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* **Çözüm Gezgini'nde,** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin.  

* **İstemci Tarafı Kitaplığı Ekle** iletişim kutusunda, **Sağlayıcı** **unpkg'ı**seçin. 

* **Kitaplık**için, önizleme olmayan en son sürümü girin `@microsoft/signalr@3`ve seçin.  

  ![İstemci-Side Kitaplığı ekle iletişim kutusu - kitaplık seçin](signalr/_static/2.x/libman1.png)   

* **Belirli dosyaları seçin,** *dist/tarayıcı* klasörünü genişletin ve *signalr.js* ve *signalr.min.js'i*seçin. 

* **Hedef Konumu** *wwwroot/lib/signalr/* olarak ayarlayın ve **Yükle'yi**seçin.    

  ![İstemci Tarafı Kitaplığı iletişim kutusu ekle - dosyaları ve hedef seçin](signalr/_static/2.x/libman2.png) 

  LibMan bir *wwwroot/lib/signalr* klasörü oluşturur ve seçili dosyaları kopyalar.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Tümleşik terminalde, LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın. Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametreler aşağıdaki seçenekleri belirtir: 
  * Unpkg sağlayıcısını kullanın. 
  * Dosyaları *wwwroot/lib/signalr* hedefine kopyalayın.    
  * Yalnızca belirtilen dosyaları kopyalayın.  

  Çıktı aşağıdaki örnek gibi görünür:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)   

* **Terminalde,** LibMan'ı yüklemek için aşağıdaki komutu çalıştırın. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Proje klasörüne *(SignalRChat.csproj* dosyasını içeren dosya) gidin. 

* LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametreler aşağıdaki seçenekleri belirtir: 
  * Unpkg sağlayıcısını kullanın. 
  * Dosyaları *wwwroot/lib/signalr* hedefine kopyalayın.    
  * Yalnızca belirtilen dosyaları kopyalayın.  

  Çıktı aşağıdaki örnek gibi görünür:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>SignalR hub'ı oluşturma 

*Hub,* istemci-sunucu iletişimini işleyen üst düzey bir ardışık hatlar olarak hizmet veren bir sınıftır.   

* SignalRChat proje klasöründe bir *Hubs* klasörü oluşturun.    

* *Hub'lar* klasöründe, aşağıdaki koda sahip *ChatHub.cs* bir dosya oluşturun: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Sınıf `ChatHub` SinyalR `Hub` sınıfından devralır. Sınıf `Hub` bağlantıları, grupları ve iletileri yönetir.    

  Yöntem, `SendMessage` tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir. Yöntemi çağıran JavaScript istemci kodu daha sonra öğreticide gösterilir. SignalR kodu maksimum ölçeklenebilirlik sağlamak için asynchronous olduğunu.  

## <a name="configure-signalr"></a>SignalR'i yapılandır    

SignalR sunucusu SignalR isteklerini SignalR'a iletecek şekilde yapılandırılmalıdır.  

* *Startup.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Bu değişiklikler, Core bağımlılık enjeksiyon sistemi ve ara yazılım boru hattı ASP.NET SignalR ekleyin.    

## <a name="add-signalr-client-code"></a>SignalR istemci kodu ekle  

* *Pages\Index.cshtml'deki* içeriği aşağıdaki kodla değiştirin:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Yukarıdaki kod:   

  * Ad ve ileti metni için metin kutuları ve gönder düğmesi oluşturur.  
  * SignalR hub'ından alınan iletileri görüntülemek `id="messagesList"` için bir liste oluşturur. 
  * SignalR'a ve bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna komut dosyası başvurularını içerir.  

* *wwwroot/js* klasöründe, aşağıdaki kodu içeren bir *chat.js* dosyası oluşturun:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Yukarıdaki kod:   

  * Bir bağlantı oluşturur ve başlatır.    
  * Gönder düğmesine, hub'a ileti gönderen bir işleyici ekler. 
  * Bağlantı nesnesine hub'dan ileti alan ve bunları listeye ekleyen bir işleyici ekler.  

## <a name="run-the-app"></a>Uygulamayı çalıştırma  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Hata ayıklama olmadan uygulamayı çalıştırmak için **CTRL+F5** tuşuna basın.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Tümleşik terminalde aşağıdaki komutu çalıştırın:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Menüden, **Hata Ayıklama olmadan Çalıştır > Başlat'ı**seçin.

---

* URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.

* Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **İleti Gönder** düğmesini seçin.  

  Ad ve ileti her iki sayfada anında görüntülenir.   

  ![SignalRörnek uygulama](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Uygulama çalışmıyorsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin. HTML ve JavaScript kodunuzla ilgili hatalar görebilirsiniz. Örneğin, *signalr.js'yi* yönlendirilmiş ten farklı bir klasöre koyduğunuzu varsayalım. Bu durumda, bu dosyaya yapılan başvuru çalışmaz ve konsolda bir 404 hatası görürsünüz.   
> ![signalr.js hata bulunamadı](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Ek kaynaklar 
* [Bu öğretici Youtube sürümü](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
