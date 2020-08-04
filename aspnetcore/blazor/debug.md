---
title: Hata ayıklama ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: cb0a8737fb975db285986d18b995e488f09580e8
ms.sourcegitcommit: 37f6f2e13ceb4eae268d20973d76e4b83acf6a24
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526296"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Hata ayıklama ASP.NET CoreBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssemblyuygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz. Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.

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

## <a name="prerequisites"></a>Önkoşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Google Chrome (sürüm 70 veya üzeri) (varsayılan)
* Microsoft Edge (sürüm 80 veya üzeri)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir

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

## <a name="visual-studio"></a>Visual Studio

Blazor WebAssemblyVisual Studio 'da bir uygulamada hata ayıklamak için:

1. Yeni ASP.NET Core barındırılan bir Blazor WebAssembly uygulama oluşturun.
1. Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
1. Metodunda bir kesme noktası ayarlayın `Pages/Counter.razor` `IncrementCount` .
1. **`Counter`** Sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. `currentCount`Yereller penceresindeki alanın değerini gözden geçirin:

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

Uygulamanızda hata ayıklarken Blazor WebAssembly , sunucu kodunuzda hata ayıklaması de yapabilirsiniz:

1. İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .
1. **`Fetch Data`** `FetchData` Bir http isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sekmeye gidin:

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Tek başına hata ayıklaBlazor WebAssembly

1. Tek başına Blazor WebAssembly uygulamayı vs Code açın.

   Hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğu aşağıdaki bildirimi alabilirsiniz:
   
   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   Bildirimi alırsanız:

   * [Visual Studio Code uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en son C# ' nin yüklü olduğunu doğrulayın. Yüklü uzantıları denetlemek için, menü çubuğundan **uzantıları görüntüle**' yi açın  >  **Extensions** veya **etkinlik** kenar çubuğunda **Uzantılar** simgesini seçin.
   * JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın. Menü çubuğundan (**Dosya**  >  **tercihleri**  >  **ayarları**) ayarları açın. Anahtar sözcüklerini kullanarak arama yapın `debug preview` . Arama sonuçlarında, **hata ayıkla > JavaScript: önizleme kullan** onay kutusunun işaretli olduğundan emin olun.
   * Pencereyi yeniden yükleyin.

1. <kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.

1. İstendiğinde, hata ayıklamayı başlatmak için ** Blazor WebAssembly Hata Ayıkla** seçeneğini belirleyin.

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.

1. Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Barındırılan hata ayıklamaBlazor WebAssembly

1. Barındırılan Blazor WebAssembly uygulamanın çözüm klasörünü vs Code açın.

1. Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir. **Evet**’i seçin.

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Pencerenin üst kısmındaki komut paletinde barındırılan çözüm içindeki *sunucu* projesini seçin.

`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.

### <a name="attach-to-an-existing-debugging-session"></a>Varolan bir hata ayıklama oturumuna Ekle

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

### <a name="launch-configuration-options"></a>Yapılandırma seçeneklerini Başlat

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

### <a name="example-launch-configurations"></a>Örnek başlatma yapılandırması

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Tek başına bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

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

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Microsoft Edge ile barındırılan bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

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

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıkla

1. Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.

1. Bir tarayıcı başlatın ve uygulamanın URL 'sine gidin (örneğin, `https://localhost:5001` ).

1. Tarayıcıda <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşlarına basarak uzaktan hata ayıklamayı yorum yapmaya çalışın.

   Tarayıcı, varsayılan olmayan bir uzaktan hata ayıklama etkinken çalışıyor olmalıdır. Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası, tarayıcıyı hata ayıklama bağlantı noktası açık olarak başlatma yönergeleriyle birlikte işlenir. Tarayıcınızla ilgili yönergeleri izleyerek yeni bir tarayıcı penceresi açılır. Önceki tarayıcı penceresini kapatın.

1. Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu (<kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>) yeni bir hata ayıklayıcı sekmesi açar.

1. Bir süre sonra, **kaynaklar** sekmesi, uygulama içindeki .net derlemelerinin bir listesini gösterir `file://` .

1. Bileşen kodunda ( `.razor` Dosyalar) ve C# kod dosyalarında ( `.cs` ), kod yürütüldüğünde ayarladığınız kesme noktaları isabet edilir. Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.

Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler. Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder. Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).

## <a name="browser-source-maps"></a>Tarayıcı kaynağı eşlemeleri

Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir. Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez. Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.

## <a name="troubleshoot"></a>Sorun giderme

Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:

* **Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın. Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.
* ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel**' de gereklidir. Bu, Visual Studio için varsayılan ayardır. Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.

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
