---
title: Hata ayıklama ASP.NET Core Blazor webassembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84239224"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Hata ayıklama ASP.NET Core Blazor webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorKmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir. Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.

Kullanılabilir senaryolar şunlardır:

* Kesme noktaları ayarlayın ve kaldırın.
* Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).
* Kod üzerinden tek adımlı (<kbd>F10</kbd>).
* Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.
* *Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.
* JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.

Şimdilik şunları *yapamazsınız*:

* İşlenmemiş özel durumların üzerine bölün.
* Uygulamanın başlatılması sırasında isabet kesme noktaları.

Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.

## <a name="prerequisites"></a>Ön koşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Microsoft Edge (sürüm 80 veya üzeri)
* Google Chrome (sürüm 70 veya üzeri)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir

Mevcut bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için Blazor , başlangıç projesindeki *launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Özelliği:

* IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.
* Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.

`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.

## <a name="visual-studio"></a>Visual Studio

BlazorVisual Studio 'da bir webassembly uygulamasında hata ayıklamak için:

1. Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.
1. Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
1. Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın `IncrementCount` .
1. **Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. `currentCount`Yereller penceresindeki alanın değerini gözden geçirin:

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

BlazorWebassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:

1. İçindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .
1. Bir HTTP isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin `FetchData` :

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

BlazorVisual Studio Code bir webassembly uygulamasında hata ayıklamak için:
 
[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .

![Uzantılar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Tek başına Blazor webassembly hatalarını ayıkla

1. BlazorVs Code ' de tek başına webassembly uygulamasını açın.

   Aşağıdaki bildirimi, hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu alırsanız:
   
   * Doğru uzantıların yüklü olduğunu doğrulayın.
   * JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.
   * Pencereyi yeniden yükleyin.

   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.

1. İstendiğinde, hata ayıklamayı başlatmak için ** Blazor webassembly hata ayıklama** seçeneğini belirleyin.

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.

1. Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Barındırılan Blazor webassembly hata ayıklaması

1. Barındırılan Blazor webassembly uygulamasını vs Code açın.

1. Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir. **Evet**' i seçin.

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Seçim penceresinde barındırılan çözüm içinde *sunucu* projesini seçin.

Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir *Launch. JSON* dosyası oluşturulur.

### <a name="attach-to-an-existing-debugging-session"></a>Varolan bir hata ayıklama oturumuna Ekle

Çalışan bir uygulamaya eklemek için Blazor aşağıdaki yapılandırmaya sahip bir *Launch. JSON* dosyası oluşturun:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir. Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.

### <a name="launch-configuration-options"></a>Yapılandırma seçeneklerini Başlat

Hata ayıklama türü için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `blazorwasm` .

| Seçenek    | Açıklama |
| --------- | ----------- |
| `request` | Bir `launch` webassembly uygulamasına bir hata ayıklama oturumu başlatmak Blazor veya eklemek veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın. |
| `url`     | Hata ayıklanırken tarayıcıda açılacak URL. Varsayılan olarak olur `https://localhost:5001` . |
| `browser` | Hata ayıklama oturumu için başlatılacak tarayıcı. Ayarlanan `edge` veya `chrome`. Varsayılan olarak olur `chrome` . |
| `trace`   | JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır. `true`Günlük oluşturmak için olarak ayarlayın. |
| `hosted`  | `true`Barındırılan webassembly uygulaması başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor . |
| `webRoot` | Web sunucusunun mutlak yolunu belirtir. Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır. |
| `timeout` | Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı. Varsayılan değer 30.000 milisaniyedir (30 saniye). |
| `program` | Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru. `hosted`İse ayarlanmalıdır `true` . |
| `cwd`     | Üzerinde uygulamayı başlatmak için çalışma dizini. `hosted`İse ayarlanmalıdır `true` . |
| `env`     | Başlatılan işleme sağlanacak ortam değişkenleri. Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` . |

### <a name="example-launch-configurations"></a>Örnek başlatma yapılandırması

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Tek başına Blazor webassembly uygulamasını başlatma ve hata ayıklama

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Belirtilen URL 'de çalışan bir uygulamaya iliştirme

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Barındırılan Blazor webassembly uygulamasını başlatma ve hata ayıklama

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıkla

1. Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.

1. <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.

1. Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır. Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur. Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir. *Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.

Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir. Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs* / *. Razor* kaynak dosyalarını bulun. Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir. Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.

Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler. Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder. Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).

## <a name="browser-source-maps"></a>Tarayıcı kaynağı eşlemeleri

Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir. Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez. Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.

## <a name="troubleshoot"></a>Sorun giderme

Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:

* **Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın. Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.
* ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
