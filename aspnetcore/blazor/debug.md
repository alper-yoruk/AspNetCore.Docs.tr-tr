---
title: Hata ayıklama ASP.NET Core Blazor WebAssembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550523"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Hata ayıklama ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly uygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz. Ayrıca, aşağıdaki tümleşik geliştirme ortamlarını (IDEs) kullanarak uygulamanızda hata ayıklaması yapabilirsiniz:

* Visual Studio
* Mac için Visual Studio
* Visual Studio Code

Kullanılabilir senaryolar şunlardır:

* Kesme noktaları ayarlayın ve kaldırın.
* Uygulamaları, hata ayıklama desteğiyle birlikte çalıştırın.
* Kodda tek adımlı.
* Ides 'teki bir klavye kısayoluyla kod yürütmeyi sürdürür.
* *Yereller* penceresinde, yerel değişkenlerin değerlerini gözlemleyin.
* JavaScript ve .NET arasındaki çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.

Şimdilik şunları *yapamazsınız* :

* İşlenmemiş özel durumların üzerine bölün.
* Hata ayıklama proxy 'si çalışmadan önce uygulama başlatılırken kesme noktaları isabet edin. Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.
* Yerel olmayan senaryolarda hata ayıklama (örneğin, [Linux Için Windows alt sistemi (WSL)](/windows/wsl/) veya [Visual Studio codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).

## <a name="prerequisites"></a>Ön koşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Google Chrome (sürüm 70 veya üzeri) (varsayılan)
* Microsoft Edge (sürüm 80 veya üzeri)

Güvenlik duvarları veya proxy 'lerin hata ayıklama proxy 'si (işlem) ile iletişimi engellemediğinden emin olun `NodeJS` . Daha fazla bilgi için [güvenlik duvarı yapılandırması](#firewall-configuration) bölümüne bakın.

Mac için Visual Studio Sürüm 8,8 (derleme 1532) veya üzeri gerektirir:

1. Microsoft 'ta **Mac için Visual Studio indir** düğmesini seçerek en son Mac için Visual Studio sürümünü yükleyin [: Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/).
1. Visual Studio içinden *Önizleme* kanalını seçin. Daha fazla bilgi için bkz. [Mac için Visual Studio Preview sürümü yüklemesi](/visualstudio/mac/install-preview).

> [!NOTE]
> MacOS üzerinde Apple Safari Şu anda desteklenmemektedir.

## <a name="enable-debugging"></a>Hata ayıklamayı etkinleştir

Mevcut bir uygulamada hata ayıklamayı etkinleştirmek için Blazor WebAssembly , `launchSettings.json` Başlangıç projesindeki dosyayı her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Dosya güncelleştirildikten sonra, `launchSettings.json` aşağıdaki örneğe benzer şekilde görünür:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Özelliği:

* IDE 'nin uygulamanın bir uygulama olduğunu algılamasını sağlar Blazor WebAssembly .
* Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.

`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Blazor WebAssemblyVisual Studio 'da bir uygulamada hata ayıklamak için:

1. Yeni ASP.NET Core barındırılan bir Blazor WebAssembly uygulama oluşturun.
1. Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.

   > [!NOTE]
   > **Hata ayıklama olmadan Başlat** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez. Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.

1. `*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .
1. Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin.
1. Visual Studio 'da, `currentCount` **Yereller** penceresindeki alanın değerini inceleyin.
1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

Bir uygulamada hata ayıklarken Blazor WebAssembly , sunucu kodunda hata ayıklama de yapabilirsiniz:

1. İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .
1. `Fetch Data` `FetchData` Sunucuya bir http isteği vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sayfaya gidin.
1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve içindeki sunucudaki kesme noktasına gidin `WeatherForecastController` .
1. Yürütmenin devam etmesine izin vermek için <kbd>F5</kbd> tuşuna basın ve tarayıcıda işlenen Hava durumu tahmin tablosunu görüntüleyin.

> [!NOTE]
> Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** . Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.

Uygulama, farklı bir [uygulama temel yolunda](xref:blazor/host-and-deploy/index#app-base-path) barındırılıyorsa `/` , `Properties/launchSettings.json` uygulamasının temel yolunu yansıtmak için ' de aşağıdaki özellikleri güncelleştirin:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` Her profilin:

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

Önceki ayarlarda yer tutucular:

* `{INSECURE PORT}`: Güvensiz bağlantı noktası. Rastgele bir değer varsayılan olarak sağlanır, ancak özel bir bağlantı noktasına izin verilir.
* `{APP BASE PATH}`: Uygulamanın temel yolu.
* `{SECURE PORT}`: Güvenli bağlantı noktası. Rastgele bir değer varsayılan olarak sağlanır, ancak özel bir bağlantı noktasına izin verilir.
* `{PROFILE 1, 2, ... N}`: Ayarlar profillerini başlatın. Genellikle, bir uygulama varsayılan olarak birden fazla profil belirtir (örneğin, IIS Express için bir profil ve Kestrel Server tarafından kullanılan bir proje profili).

Aşağıdaki örneklerde, uygulama şu `/OAT` şekilde yapılandırılmış bir uygulama temel yolu ile konumunda barındırılır `wwwroot/index.html` `<base href="/OAT/">` :

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Uygulamalar için özel bir uygulama temel yolu kullanma hakkında daha fazla bilgi için Blazor WebAssembly bkz <xref:blazor/host-and-deploy/index#app-base-path> ..

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Tek başına hata ayıkla Blazor WebAssembly</h2>

1. Tek başına Blazor WebAssembly uygulamayı vs Code açın.

   Hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu belirten bir bildirim alabilirsiniz:

   > Uygulamalarda hata ayıklamak için ek kurulum gereklidir Blazor WebAssembly .

   Bildirimi alırsanız:

   * [Visual Studio Code uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en son C# ' nin yüklü olduğunu doğrulayın. Yüklü uzantıları denetlemek için, menü çubuğundan **uzantıları görüntüle** ' yi açın  >  **Extensions** veya **etkinlik** kenar çubuğunda **Uzantılar** simgesini seçin.
   * JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın. Menü çubuğundan ( **Dosya**  >  **tercihleri**  >  **ayarları** ) ayarları açın. Anahtar sözcüklerini kullanarak arama yapın `debug preview` . Arama sonuçlarında, **hata ayıkla > JavaScript: önizleme kullan** onay kutusunun işaretli olduğundan emin olun. Önizleme hata ayıklamayı etkinleştirme seçeneği yoksa, VS Code en son sürümüne yükseltin veya [JavaScript hata ayıklayıcı uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 'nı (vs Code 1,46 veya önceki bir sürümü) yüklersiniz.
   * Pencereyi yeniden yükleyin.

1. <kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.

   > [!NOTE]
   > **Hata ayıklama olmadan Başlat** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez. Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.

1. İstendiğinde, hata ayıklamayı başlatmak için **Blazor WebAssembly Hata Ayıkla** seçeneğini belirleyin.

1. Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.

1. `*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .

1. Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin.

> [!NOTE]
> Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** . Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Barındırılan hata ayıklama Blazor WebAssembly

1. Barındırılan Blazor WebAssembly uygulamanın çözüm klasörünü vs Code açın.

1. Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir. **Evet** ’i seçin.

   > ' {APPLICATION NAME} ' içindeki derleme ve hata ayıklama için gerekli varlıklar eksik. Bunları ekleyin mi?

1. Pencerenin üst kısmındaki komut paletinde barındırılan çözüm içindeki *sunucu* projesini seçin.

`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.

## <a name="attach-to-an-existing-debugging-session"></a>Varolan bir hata ayıklama oturumuna Ekle

Çalışan bir uygulamaya eklemek için Blazor `launch.json` aşağıdaki yapılandırmaya sahip bir dosya oluşturun:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir. Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.

## <a name="launch-configuration-options"></a>Yapılandırma seçeneklerini Başlat

`blazorwasm`Hata ayıklama türü () için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `.vscode/launch.json` .

| Seçenek    | Açıklama |
| --------- | ----------- |
| `request` | `launch`Bir uygulamaya hata ayıklama oturumu başlatmak ve eklemek Blazor WebAssembly veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın. |
| `url`     | Hata ayıklanırken tarayıcıda açılacak URL. Varsayılan olarak olur `https://localhost:5001` . |
| `browser` | Hata ayıklama oturumu için başlatılacak tarayıcı. Ayarlanan `edge` veya `chrome`. Varsayılan olarak olur `chrome` . |
| `trace`   | JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır. `true`Günlük oluşturmak için olarak ayarlayın. |
| `hosted`  | `true`Barındırılan bir uygulama başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor WebAssembly . |
| `webRoot` | Web sunucusunun mutlak yolunu belirtir. Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır. |
| `timeout` | Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı. Varsayılan değer 30.000 milisaniyedir (30 saniye). |
| `program` | Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru. `hosted`İse ayarlanmalıdır `true` . |
| `cwd`     | Üzerinde uygulamayı başlatmak için çalışma dizini. `hosted`İse ayarlanmalıdır `true` . |
| `env`     | Başlatılan işleme sağlanacak ortam değişkenleri. Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` . |

## <a name="example-launch-configurations"></a>Örnek başlatma yapılandırması

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Tek başına bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Belirtilen URL 'de çalışan bir uygulamaya iliştirme

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Microsoft Edge ile barındırılan bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

Tarayıcı yapılandırması varsayılan olarak Google Chrome olarak belirlenmiştir. Hata ayıklama için Microsoft Edge kullanılırken, `browser` olarak ayarlayın `edge` . Google Chrome 'ı kullanmak için `browser` seçeneği ayarlamayın veya seçeneğin değerini olarak ayarlayın `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

Yukarıdaki örnekte, `MyHostedApp.Server.dll` *sunucu* uygulamasının derlemesi olur. `.vscode`Klasörü `Client` ,, `Server` ve klasörlerinin yanında çözüm klasöründe bulunur `Shared` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Mac için Visual Studio bir uygulamada hata ayıklamak için Blazor WebAssembly :

1. Yeni ASP.NET Core barındırılan bir Blazor WebAssembly uygulama oluşturun.
1. <kbd>&#8984;</kbd> + Uygulamayı hata ayıklayıcıda çalıştırmak için&#8984;<kbd>&#8617;</kbd> tuşuna basın.

   > [!NOTE]
   > **Hata ayıklama olmadan Başlat** ( <kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) desteklenmez. Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.

   > [!IMPORTANT]
   > Google Chrome veya Microsoft Edge, hata ayıklama oturumunun seçili tarayıcısı olmalıdır.

1. `*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .
1. Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin:
1. Visual Studio 'da, `currentCount` **Yereller** penceresindeki alanın değerini inceleyin.
1. <kbd>&#8984;</kbd> + Yürütmeye devam etmek için&#8984;<kbd>&#8617;</kbd> tuşuna basın.

Bir uygulamada hata ayıklarken Blazor WebAssembly , sunucu kodunda hata ayıklama de yapabilirsiniz:

1. İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .
1. `Fetch Data` `FetchData` Sunucuya bir http isteği vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sayfaya gidin.
1. <kbd>&#8984;</kbd> + Yürütmeye devam etmek için&#8984;<kbd>&#8617;</kbd> tuşuna basın ve ardından içindeki sunucudaki kesme noktasına gidin `WeatherForecastController` .
1. <kbd>&#8984;</kbd> + Yürütmenin devam etmesine izin vermek ve tarayıcıda işlenen Hava durumu tahmin tablosuna bakmak için&#8984;<kbd>&#8617;</kbd> tuşuna basın.

> [!NOTE]
> Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** . Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.

Daha fazla bilgi için bkz. [Mac için Visual Studio Ile hata ayıklama](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıkla

*Bu bölümdeki kılavuz, Google Chrome ve Windows üzerinde çalışan Microsoft Edge için geçerlidir.*

1. Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.

1. Bir tarayıcı başlatın ve uygulamanın URL 'sine gidin (örneğin, `https://localhost:5001` ).

1. Tarayıcıda <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>d</kbd>tuşlarına basarak uzaktan hata ayıklamayı yorum yapmaya çalışın.

   Tarayıcı, varsayılan olmayan bir uzaktan hata ayıklama etkinken çalışıyor olmalıdır. Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası, tarayıcıyı hata ayıklama bağlantı noktası açık olarak başlatma yönergeleriyle birlikte işlenir. Tarayıcınızla ilgili yönergeleri izleyerek yeni bir tarayıcı penceresi açılır. Önceki tarayıcı penceresini kapatın.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Tarayıcı uzaktan hata ayıklama etkinken çalışmaya başladıktan sonra, önceki adımda hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar.

1. Bir süre sonra, **kaynaklar** sekmesi, uygulama içindeki .net derlemelerinin bir listesini gösterir `file://` .

1. Bileşen kodunda ( `.razor` Dosyalar) ve C# kod dosyalarında ( `.cs` ), kod yürütüldüğünde ayarladığınız kesme noktaları isabet edilir. Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.

Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler. Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder. Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).

## <a name="browser-source-maps"></a>Tarayıcı kaynağı eşlemeleri

Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir. Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez. Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.

## <a name="firewall-configuration"></a>Güvenlik duvarı yapılandırması

Bir güvenlik duvarı hata ayıklama proxy 'si ile iletişimi engelliyorsa, tarayıcı ve işlem arasında iletişime izin veren bir güvenlik duvarı özel durumu kuralı oluşturun `NodeJS` .

> [!WARNING]
> Güvenlik güvenliği güvenlik açıklarını oluşturmaktan kaçınmak için bir güvenlik duvarı yapılandırmasının değiştirilmesi dikkatli yapılmalıdır. Güvenlik kılavuzunu dikkatle uygulayın, en iyi güvenlik uygulamalarını izleyin ve güvenlik duvarının üreticisi tarafından verilen uyarıları dikkate edin.
>
> İşlemle iletişimi açmaya izin verme `NodeJS` :
>
> * Güvenlik duvarının özelliklerine ve yapılandırmasına bağlı olarak düğüm sunucusunu herhangi bir bağlantıda açar.
> * Ağınıza bağlı olarak riskli olabilir.
> * **Yalnızca geliştirici makinelerinde önerilir.**
>
> Mümkünse, yalnızca `NodeJS` **Güvenilen veya özel ağlardaki** işlemle açık iletişime izin verin.

[Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) yapılandırma kılavuzu için bkz. [bir gelen program veya hizmet kuralı oluşturma](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule). Daha fazla bilgi için Windows Güvenlik Duvarı belge kümesindeki [Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) ve ilgili makalelere bakın.

## <a name="troubleshoot"></a>Sorun giderme

Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:

* **Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın. Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.
* ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel** ' de gereklidir. Bu, Visual Studio için varsayılan ayardır. Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.
* Ortamınız bir HTTP proxy kullanıyorsa, `localhost` proxy atlama ayarlarına dahil edildiğinden emin olun. Bu, `NO_PROXY` ortam değişkeni şu şekilde ayarlanarak yapılabilir:
  * `launchSettings.json`Projenin dosyası.
  * Kullanıcı veya sistem ortamı değişkenleri düzeyinde tüm uygulamalara uygulanır. Bir ortam değişkeni kullanırken, değişikliğin etkili olması için Visual Studio 'Yu yeniden başlatın.
* Güvenlik duvarları veya proxy 'lerin hata ayıklama proxy 'si (işlem) ile iletişimi engellemediğinden emin olun `NodeJS` . Daha fazla bilgi için [güvenlik duvarı yapılandırması](#firewall-configuration) bölümüne bakın.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>`OnInitialized{Async}`İsabet bulunmayan kesme noktaları

BlazorÇerçevenin hata ayıklama proxy 'sinin başlatılması kısa bir süre sürer, bu nedenle [ `OnInitialized{Async}` yaşam döngüsü yöntemindeki](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları isabet edemeyebilir. Hata ayıklama proxy 'sine, kesme noktasından önce başlamak için bir süre önce, Yöntem gövdesinin başlangıcında bir gecikme eklemeniz önerilir. Uygulamanın yayın derlemesi için gecikmeye izin olmadığından emin olmak için bir [ `if` derleyici yönergesine](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) göre gecikmeyi dahil edebilirsiniz.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Visual Studio (Windows) zaman aşımı

Visual Studio, zaman aşımına ulaşılmış olduğunu belirten hata ayıklama bağdaştırıcısının başarısız bir özel durum oluşturursa, zaman aşımını bir kayıt defteri ayarıyla ayarlayabilirsiniz:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

`{TIMEOUT}`Önceki komutta yer tutucu milisaniyedir. Örneğin, bir dakika olarak atanır `60000` .
