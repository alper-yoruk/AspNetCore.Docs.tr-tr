---
title: TypeScript SignalR ve Webpack ile ASP.NET Core'u kullanma
author: ssougnez
description: Bu eğitimde, Webpack'i, istemcisi SignalR TypeScript'te yazılmış bir ASP.NET Core web uygulamasını paketleyip oluşturmak üzere yapılandırmış sınız.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511346"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>TypeScript ve Webpack ile ASP.NET Core SignalR kullanın

Yazar: [Sébastien Sougnez](https://twitter.com/ssougnez) ve [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack,](https://webpack.js.org/) geliştiricilerin bir web uygulamasının istemci tarafındaki kaynaklarını paketleyip oluşturmalarını sağlar. Bu öğretici, [istemcisi TypeScript'te](https://www.typescriptlang.org/)yazılmış bir ASP.NET Core SignalR web uygulamasında Webpack'in kullanılmasını gösterir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * İskele Core SignalR uygulaması ASP.NET bir marş
> * SignalR TypeScript istemcisini yapılandırma
> * Webpack'i kullanarak yapı ardışık yapılı
> * SignalR sunucusunu yapılandırma
> * İstemci ve sunucu arasındaki iletişimi etkinleştirme

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile
* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code sürüm 1.17.1 veya sonrası için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)

---

## <a name="create-the-aspnet-core-web-app"></a>ASP.NET Core web uygulamasını oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio'yu *PATH* ortamı değişkeninde npm'yi arayacak şekilde yapılandırın. Varsayılan olarak, Visual Studio yükleme dizininde bulunan npm sürümünü kullanır. Visual Studio'da şu talimatları uygulayın:

1. Visual Studio’yu başlatın. Başlangıç penceresinde, **kodsuz Devam**et'i seçin.
1. **Araçlar** > **Seçenekleri** > **Projeler ve Çözümler** > **Web Paket Yönetimi** > **Dış Web Araçları**gidin.
1. Listeden *$(PATH)* girişini seçin. Girişi listedeki ikinci konuma taşımak için yukarı ok'u tıklatın ve **Tamam'ı**seçin.

    ![Görsel Stüdyo Yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Visual Studio yapılandırması tamamlandı.

1.  > **New**Yeni >  **Proje Dosyası**menüsü seçeneğini kullanın ve **ASP.NET Çekirdek Web Uygulaması** şablonuna gidin.**Project** **Sonraki'ni**seçin.
1. Projeye *SignalRWebPack*adını ve **Oluştur'u**seçin.
1. Hedef çerçeve açılır tarafından *.NET Core'u* seçin ve çerçeve seçici açılır ASP.NET *Core 3.1'i* seçin. Boş **şablonu** seçin ve **Oluştur'u**seçin.

`Microsoft.TypeScript.MSBuild` Paketi projeye ekleyin:

1. **Çözüm Gezgini'nde** (sağ bölme), proje düğümüne sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin. **Gözat** sekmesinde, paketi `Microsoft.TypeScript.MSBuild`yüklemek için sağ tarafta Yükle'yi arayın ve ardından **Yükle'yi** tıklatın.

Visual Studio, **NuGet**paketini Solution Explorer'daki **Bağımlılıkdüğümün** altına ekler ve projede TypeScript derlemesini etkinleştirir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Entegre Terminal'de**aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* Komut, `dotnet new` *SignalRWebPack* dizininde boş bir ASP.NET Core web uygulaması oluşturur.
* Komut, `code` Visual Studio Code'un geçerli örneğinde *SignalRWebPack* klasörünü açar.

**Entegre Terminalde**aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Önceki komut, projede TypeScript derlemesini etkinleştiren [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) paketini ekler.

---

## <a name="configure-webpack-and-typescript"></a>Webpack ve TypeScript yapılandırma

Aşağıdaki adımlar, TypeScript'in JavaScript'e dönüştürülmesini ve istemci tarafındaki kaynakların birbirlemasını yapılandırır.

1. *Package.json* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:

    ```console
    npm init -y
    ```

1. Vurgulanan özelliği *package.json* dosyasına ekleyin ve dosya değişikliklerini kaydedin:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    `private` Bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde özelliği ayarlama.

1. Gerekli npm paketlerini yükleyin. Proje kökünden aşağıdaki komutu çalıştırın:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Dikkat edilmesi gereken bazı komut ayrıntıları:

    * Her paket adı `@` için bir sürüm numarası işareti izler. npm bu özel paket sürümlerini yükler.
    * Seçenek, `-E` npm'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *package.json'a*yazma varsayılan davranışını devre dışı kılabilir. Örneğin, `"webpack": "4.41.5"` `"webpack": "^4.41.5"`". Bu seçenek, yeni paket sürümlerinde istenmeyen yükseltmeleri önler.

    Daha fazla ayrıntı için [npm-install](https://docs.npmjs.com/cli/install) dokümanlarına bakın.

1. `scripts` *Package.json* dosyasının özelliğini aşağıdaki kodla değiştirin:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Komut dosyalarının bazı açıklamaları:

    * `build`: İstemci tarafındaki kaynakları geliştirme modunda paketler ve dosya değişikliklerini izler. Dosya izleyicisi, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur. Bu `mode` seçenek, ağaç sallayarak ve minification gibi üretim optimizasyonları devre dışı bırakmaz. Yalnızca `build` geliştirmede kullanılır.
    * `release`: İstemci tarafındaki kaynakları üretim modunda paketler.
    * `publish`: İstemci tarafındaki kaynakları üretim modunda bir araya getirmek için komut dosyasını `release` çalıştırır. .NET Core CLI'nin uygulamayı yayımlamak için [yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çağırır.

1. Proje kökünde *webpack.config.js*adlı bir dosya oluşturun ve aşağıdaki kodu girin:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Önceki dosya Webpack derlemesini yapılandırır. Dikkat edilmesi gereken bazı yapılandırma ayrıntıları:

    * `output` Özellik, *dist'in*varsayılan değerini geçersiz kılar. Paket bunun yerine *wwwroot* dizininde yayılır.
    * Dizi `resolve.extensions` SignalR istemcijavascript almak için *.js* içerir.

1. Projenin istemci tarafındaki varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.

1. Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Önceki HTML, ana sayfanın ortak biçimlendirmesini tanımlar.

1. Yeni bir *src/css* dizini oluşturun. Amacı projenin *.css* dosyalarını depolamaktır.

1. Aşağıdaki CSS ile *src/css/main.css* oluşturun:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Önceki *main.css* dosyası uygulama stilleri.

1. Aşağıdaki JSON ile *src/tsconfig.json* oluşturun:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Önceki kod [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretmek için TypeScript derleyicisi yapılandırır.

1. Aşağıdaki kodla *src/index.ts* oluşturun:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Önceki TypeScript, DOM öğelerine başvuru alır ve iki olay işleyicisi bağlar:

    * `keyup`: Kullanıcı `tbMessage`textbox'ta yazdığında bu olay ateşler. Kullanıcı `send` **Enter** tuşuna bastığında işlev çağrılır.
    * `click`: Kullanıcı **Gönder** düğmesini tıklattığında bu olay ateşler. `send` işlevi çağrılır.

## <a name="configure-the-app"></a>Uygulamayı yapılandırma

1. In `Startup.Configure`, [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)aramaları ekleyin.

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Önceki kod, sunucunun *index.html* dosyasını bulmasını ve sunmasını sağlar.  Dosya, kullanıcının tam URL'sini veya web uygulamasının kök URL'sini girip girmediği sunulur.

1. Sonunda, hub'a giden bir */hub* rotasını haritala. `ChatHub` `Startup.Configure` Hello World'u görüntüleyen kodu *değiştirin!* aşağıdaki satırla: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. In `Startup.ConfigureServices`, [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)arayın.

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. SignalR hub'ını depolamak için project root *SignalRWebPack/'de* *Hub adı* verilen yeni bir dizin oluşturun.

1. Hub *Hub'larını/ChatHub.c'lerini* aşağıdaki kodla oluşturun:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Başvuruyu `using` çözmek için Startup.cs dosyasının üst kısmındaaşağıdaki deyimi ekleyin: *Startup.cs* `ChatHub`

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>İstemci ve sunucu iletişimini etkinleştirme

Uygulama şu anda mesaj göndermek için temel bir form görüntüler, ancak henüz işlevsel değildir. Sunucu belirli bir rotayı dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.

1. Proje kökünde aşağıdaki komutu çalıştırın:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Önceki komut yükler:

     * İstemcinin sunucuya ileti göndermesine olanak tanıyan [SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr)istemcisi.
     * Düğüm.js türlerinin derleme zamanı denetimini sağlayan Düğüm.js için TypeScript türü tanımları.

1. Vurgulanan kodu *src/index.ts* dosyasına ekleyin:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Önceki kod sunucudan ileti almayı destekler. Sınıf, `HubConnectionBuilder` sunucu bağlantısını yapılandırmak için yeni bir oluşturucu oluşturur. İşlev `withUrl` hub URL'sini yapılandırır.

    SignalR, istemci ve sunucu arasında ileti alışverişini sağlar. Her iletinin belirli bir adı vardır. Örneğin, ada `messageReceived` sahip iletiler, iletiler bölgesinde yeni iletinin görüntülenmesinden sorumlu mantığı çalıştırabilir. Belirli bir iletiyi dinleme `on` işlevi üzerinden yapılabilir. Herhangi bir sayıda ileti adı dinlenebilir. Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye aktarmak da mümkündür. İstemci bir ileti aldıktan `div` sonra, yazarın adı ve özniteliğiyle `innerHTML` ileti içeriğiyle yeni bir öğe oluşturulur. İletileri görüntüleyen ana `div` öğeye eklenir.

1. Artık istemci bir ileti alabildiği için, ileti gönderecek şekilde yapılandırın. Vurgulanan kodu *src/index.ts* dosyasına ekleyin:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    WebSockets bağlantısı üzerinden ileti göndermek, `send` yöntemi çağırmayı gerektirir. Yöntemin ilk parametresi ileti adıdır. İleti verileri diğer parametrelerde yaşar. Bu örnekte, sunucuya `newMessage` olarak tanımlanan bir ileti gönderilir. İleti, bir metin kutusundan kullanıcı adı ve kullanıcı girişi oluşur. Gönderme çalışıyorsa, metin kutusu değeri temizlenir.

1. `NewMessage` yönetimini `ChatHub` sınıfına ekleyin:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Önceki kod yayınları, sunucu bunları aldıktan sonra bağlı tüm kullanıcılara iletiler aldı. Tüm iletileri almak için `on` genel bir yöntemolması gereksizdir. İleti adının adını taşıyan bir yöntem yeterlidir.

    Bu örnekte, TypeScript istemcisi `newMessage`olarak tanımlanan bir ileti gönderir. C# `NewMessage` yöntemi istemci tarafından gönderilen verileri bekler. Bir arama [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [için Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)yapılır. Alınan iletiler hub'a bağlı tüm istemcilere gönderilir.

## <a name="test-the-app"></a>Uygulamayı test edin

Uygulamanın aşağıdaki adımlarla çalıştığını doğrulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Webpack'i *sürüm* modunda çalıştırın. Paket **Yöneticisi Konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın. Proje kökünde değilseniz, komutu girmeden önce girin. `cd SignalRWebPack`

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Hata ayıklama aracısını takmadan uygulamayı tarayıcıda başlatmak için**hata ayıklama yapmadan** Hata **Ayıklama** > Başlat'ı seçin. *wwwroot/index.html* dosyası ' `http://localhost:<port_number>`nda servis edilir.

   Derleme hataları alırsanız, çözümü kapatmayı ve yeniden açmayı deneyin. 

1. Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın. URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın. Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Proje kökünde aşağıdaki komutu çalıştırarak Webpack'i *sürüm* modunda çalıştırın:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Proje kökünde aşağıdaki komutu uygulayarak uygulamayı oluşturun ve çalıştırın:

    ```dotnetcli
    dotnet run
    ```

    Web sunucusu uygulamayı başlatır ve localhost'ta kullanılabilir hale getirir.

1. Bir tarayıcı `http://localhost:<port_number>`yı aç. *wwwroot/index.html* dosyası servis edilir. URL'yi adres çubuğundan kopyalayın.

1. Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın. URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın. Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.

---

![her iki tarayıcı penceresinde görüntülenen ileti](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile
* [.NET Core SDK 2.2 veya sonrası](https://dotnet.microsoft.com/download/dotnet-core)
* [Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 veya sonrası](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code sürüm 1.17.1 veya sonrası için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Npm](https://www.npmjs.com/) ile [Düğüm.js](https://nodejs.org/)

---

## <a name="create-the-aspnet-core-web-app"></a>ASP.NET Core web uygulamasını oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio'yu *PATH* ortamı değişkeninde npm'yi arayacak şekilde yapılandırın. Varsayılan olarak, Visual Studio yükleme dizininde bulunan npm sürümünü kullanır. Visual Studio'da şu talimatları uygulayın:

1. **Araçlar** > **Seçenekleri** > **Projeler ve Çözümler** > **Web Paket Yönetimi** > **Dış Web Araçları**gidin.
1. Listeden *$(PATH)* girişini seçin. Girişi listedeki ikinci konuma taşımak için yukarı ok'u tıklatın.

    ![Görsel Stüdyo Yapılandırması](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Visual Studio yapılandırması tamamlandı. Projeyi oluşturma nın zamanı.

1. > **New** Yeni > **Proje Dosyası** menüsü seçeneğini kullanın ve **ASP.NET Çekirdek Web Uygulaması** şablonuna gidin. **Project**
1. Projeye *SignalRWebPack*adını ve **Oluştur'u**seçin.
1. Hedef çerçeve açılır tarafından *.NET Core'u* seçin ve çerçeve seçici açılır ASP.NET *Core 2.2'yi* seçin. Boş **şablonu** seçin ve **Oluştur'u**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Entegre Terminal'de**aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet new web -o SignalRWebPack
```

.NET Core'u hedefleyen boş bir ASP.NET Core web uygulaması *SignalRWebPack* dizininde oluşturulur.

---

## <a name="configure-webpack-and-typescript"></a>Webpack ve TypeScript yapılandırma

Aşağıdaki adımlar, TypeScript'in JavaScript'e dönüştürülmesini ve istemci tarafındaki kaynakların birbirlemasını yapılandırır.

1. *Package.json* dosyası oluşturmak için proje kökünde aşağıdaki komutu çalıştırın:

    ```console
    npm init -y
    ```

1. Vurgulanan özelliği *package.json* dosyasına ekleyin:

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    `private` Bir sonraki `true` adımda paket yükleme uyarılarını engelleyecek şekilde özelliği ayarlama.

1. Gerekli npm paketlerini yükleyin. Proje kökünden aşağıdaki komutu çalıştırın:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Dikkat edilmesi gereken bazı komut ayrıntıları:

    * Her paket adı `@` için bir sürüm numarası işareti izler. npm bu özel paket sürümlerini yükler.
    * Seçenek, `-E` npm'nin [semantik sürüm](https://semver.org/) aralığı işleçlerini *package.json'a*yazma varsayılan davranışını devre dışı kılabilir. Örneğin, `"webpack": "4.29.3"` `"webpack": "^4.29.3"`". Bu seçenek, yeni paket sürümlerinde istenmeyen yükseltmeleri önler.

    Daha fazla ayrıntı için [npm-install](https://docs.npmjs.com/cli/install) dokümanlarına bakın.

1. `scripts` *Package.json* dosyasının özelliğini aşağıdaki kodla değiştirin:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Komut dosyalarının bazı açıklamaları:

    * `build`: İstemci tarafındaki kaynakları geliştirme modunda paketler ve dosya değişikliklerini izler. Dosya izleyicisi, bir proje dosyası her değiştiğinde paketin yeniden oluşturulmasına neden olur. Bu `mode` seçenek, ağaç sallayarak ve minification gibi üretim optimizasyonları devre dışı bırakmaz. Yalnızca `build` geliştirmede kullanılır.
    * `release`: İstemci tarafındaki kaynakları üretim modunda paketler.
    * `publish`: İstemci tarafındaki kaynakları üretim modunda bir araya getirmek için komut dosyasını `release` çalıştırır. .NET Core CLI'nin uygulamayı yayımlamak için [yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çağırır.

1. Proje kökünde *webpack.config.js* adlı bir dosya oluşturun ve aşağıdaki kodu girin:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Önceki dosya Webpack derlemesini yapılandırır. Dikkat edilmesi gereken bazı yapılandırma ayrıntıları:

    * `output` Özellik, *dist'in*varsayılan değerini geçersiz kılar. Paket bunun yerine *wwwroot* dizininde yayılır.
    * Dizi `resolve.extensions` SignalR istemcijavascript almak için *.js* içerir.

1. Projenin istemci tarafındaki varlıklarını depolamak için proje kökünde yeni bir *src* dizini oluşturun.

1. Aşağıdaki biçimlendirmeyle *src/index.html* oluşturun.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Önceki HTML, ana sayfanın ortak biçimlendirmesini tanımlar.

1. Yeni bir *src/css* dizini oluşturun. Amacı projenin *.css* dosyalarını depolamaktır.

1. Aşağıdaki biçimlendirme ile *src/css/main.css* oluşturun:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Önceki *main.css* dosyası uygulama stilleri.

1. Aşağıdaki JSON ile *src/tsconfig.json* oluşturun:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Önceki kod [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 uyumlu JavaScript üretmek için TypeScript derleyicisi yapılandırır.

1. Aşağıdaki kodla *src/index.ts* oluşturun:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Önceki TypeScript, DOM öğelerine başvuru alır ve iki olay işleyicisi bağlar:

    * `keyup`: Kullanıcı `tbMessage` textbox'ta yazdığında bu olay ateşler. Kullanıcı `send` **Enter** tuşuna bastığında işlev çağrılır.
    * `click`: Kullanıcı **Gönder** düğmesini tıklattığında bu olay ateşler. `send` işlevi çağrılır.

## <a name="configure-the-aspnet-core-app"></a>ASP.NET Core uygulamasını yapılandırma

1. `Startup.Configure` Yöntemde sağlanan kod *Hello World görüntüler!*. Yöntem `app.Run` çağrısını [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağrılarıyla değiştirin.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Önceki kod, kullanıcının tam URL'sini veya web uygulamasının kök URL'sini girip girmediğini sunucunun *index.html* dosyasını bulup sunmasına olanak tanır.

1. [AddSignalR'ı](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'da `Startup.ConfigureServices`arayın. Projeye SignalR hizmetlerini ekler.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Hub'a bir */hub* rotasını eşle. `ChatHub` Sonunda aşağıdaki satırları `Startup.Configure`ekleyin:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Proje kökünde Hub *adı*verilen yeni bir dizin oluşturun. Amacı, bir sonraki adımda oluşturulan SignalR hub'ını depolamaktır.

1. Hub *Hub'larını/ChatHub.c'lerini* aşağıdaki kodla oluşturun:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Başvuruyu çözmek için Startup.cs dosyanın üst kısmındaaşağıdaki kodu ekleyin: *Startup.cs* `ChatHub`

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>İstemci ve sunucu iletişimini etkinleştirme

Uygulama şu anda mesaj göndermek için basit bir form görüntüler. Bunu yapmaya çalıştığın zaman hiçbir şey olmaz. Sunucu belirli bir rotayı dinliyor, ancak gönderilen iletilerle hiçbir şey yapmıyor.

1. Proje kökünde aşağıdaki komutu çalıştırın:

    ```console
    npm install @aspnet/signalr
    ```

    Önceki komut, istemcinin sunucuya ileti göndermesine olanak tanıyan [SignalR TypeScript istemcisini](https://www.npmjs.com/package/@microsoft/signalr)yükler.

1. Vurgulanan kodu *src/index.ts* dosyasına ekleyin:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Önceki kod sunucudan ileti almayı destekler. Sınıf, `HubConnectionBuilder` sunucu bağlantısını yapılandırmak için yeni bir oluşturucu oluşturur. İşlev `withUrl` hub URL'sini yapılandırır.

    SignalR, istemci ve sunucu arasında ileti alışverişini sağlar. Her iletinin belirli bir adı vardır. Örneğin, ada `messageReceived` sahip iletiler, iletiler bölgesinde yeni iletinin görüntülenmesinden sorumlu mantığı çalıştırabilir. Belirli bir iletiyi dinleme `on` işlevi üzerinden yapılabilir. İstediğiniz sayıda ileti adını dinleyebilirsiniz. Ayrıca, yazarın adı ve alınan iletinin içeriği gibi parametreleri iletiye aktarmak da mümkündür. İstemci bir ileti aldıktan `div` sonra, yazarın adı ve özniteliğiyle `innerHTML` ileti içeriğiyle yeni bir öğe oluşturulur. Yeni ileti, iletileri `div` görüntüleyen ana öğeye eklenir.

1. Artık istemci bir ileti alabildiği için, ileti gönderecek şekilde yapılandırın. Vurgulanan kodu *src/index.ts* dosyasına ekleyin:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    WebSockets bağlantısı üzerinden ileti göndermek, `send` yöntemi çağırmayı gerektirir. Yöntemin ilk parametresi ileti adıdır. İleti verileri diğer parametrelerde yaşar. Bu örnekte, sunucuya `newMessage` olarak tanımlanan bir ileti gönderilir. İleti, bir metin kutusundan kullanıcı adı ve kullanıcı girişi oluşur. Gönderme çalışıyorsa, metin kutusu değeri temizlenir.

1. `NewMessage` yönetimini `ChatHub` sınıfına ekleyin:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Önceki kod yayınları, sunucu bunları aldıktan sonra bağlı tüm kullanıcılara iletiler aldı. Tüm iletileri almak için `on` genel bir yöntemolması gereksizdir. İleti adının adını taşıyan bir yöntem yeterlidir.

    Bu örnekte, TypeScript istemcisi `newMessage`olarak tanımlanan bir ileti gönderir. C# `NewMessage` yöntemi istemci tarafından gönderilen verileri bekler. Bir arama [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) [için Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)yapılır. Alınan iletiler hub'a bağlı tüm istemcilere gönderilir.

## <a name="test-the-app"></a>Uygulamayı test edin

Uygulamanın aşağıdaki adımlarla çalıştığını doğrulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Webpack'i *sürüm* modunda çalıştırın. Paket **Yöneticisi Konsol** penceresini kullanarak, proje kökünde aşağıdaki komutu çalıştırın. Proje kökünde değilseniz, komutu girmeden önce girin. `cd SignalRWebPack`

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Hata ayıklama aracısını takmadan uygulamayı tarayıcıda başlatmak için**hata ayıklama yapmadan** Hata **Ayıklama** > Başlat'ı seçin. *wwwroot/index.html* dosyası ' `http://localhost:<port_number>`nda servis edilir.

1. Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın. URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın. Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Proje kökünde aşağıdaki komutu çalıştırarak Webpack'i *sürüm* modunda çalıştırın:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Proje kökünde aşağıdaki komutu uygulayarak uygulamayı oluşturun ve çalıştırın:

    ```dotnetcli
    dotnet run
    ```

    Web sunucusu uygulamayı başlatır ve localhost'ta kullanılabilir hale getirir.

1. Bir tarayıcı `http://localhost:<port_number>`yı aç. *wwwroot/index.html* dosyası servis edilir. URL'yi adres çubuğundan kopyalayın.

1. Başka bir tarayıcı örneği (herhangi bir tarayıcı) açın. URL'yi adres çubuğuna yapıştırın.

1. Tarayıcıdan birini seçin, **İleti** metin kutusuna bir şey yazın ve **Gönder** düğmesini tıklatın. Benzersiz kullanıcı adı ve iletisi her iki sayfada anında görüntülenir.

---

![her iki tarayıcı penceresinde görüntülenen ileti](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
