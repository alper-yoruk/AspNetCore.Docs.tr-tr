---
title: Hata ayıklama Blazor ASP.NET Core webassembly
author: guardrex
description: Uygulamalarda hata ayıklamayı Blazor öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9acbb8e7b122a8d527d16ce33af01c2e7e7608bf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767544"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Hata ayıklama Blazor ASP.NET Core webassembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorKmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir.  Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklayabilirsiniz.

Kullanılabilir senaryolar şunlardır:

* Kesme noktaları ayarlayın ve kaldırın.
* Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).
* Kod üzerinden tek adımlı (<kbd>F10</kbd>).
* Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.
* *Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.
* JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.

Şimdilik şunları *yapamazsınız*:

* Dizileri inceleyin.
* Üyeleri incelemek için üzerine gelin.
* Yönetilen kodun içine veya dışına hata ayıklama adımı.
* Değer türlerini incelemek için tam destek vardır.
* İşlenmemiş özel durumların üzerine bölün.
* Uygulamanın başlatılması sırasında isabet kesme noktaları.
* Hizmet çalışanıyla bir uygulamada hata ayıklama.

Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.

## <a name="prerequisites"></a>Ön koşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Microsoft Edge (sürüm 80 veya üzeri)
* Google Chrome (sürüm 70 veya üzeri)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir

Hata ayıklama, ASP.NET Core 3,2 Preview 3 veya sonraki Blazor bir webassembly proje şablonu kullanılarak oluşturulan yeni projeler için otomatik olarak etkinleştirilir ([geçerli sürüm 3,2 Preview 4 ' dir](xref:blazor/get-started)).

Mevcut Blazor bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için, başlangıç projesindeki *launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki `inspectUri` özelliği içerecek şekilde güncelleştirin:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` Özelliği:

* IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.
* Betik hata ayıklama altyapısına, hata ayıklama proxy 'si aracılığıyla Blazortarayıcıya bağlanmasını söyler.

## <a name="visual-studio"></a>Visual Studio

Visual Studio 'da Blazor bir webassembly uygulamasında hata ayıklamak için:

1. Visual Studio 2019 16,6 ' nin (Preview 2 veya üzeri) [en son önizleme sürümünü yüklediğinizden](https://visualstudio.com/preview) emin olun.
1. Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.
1. Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
1. `IncrementCount` Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın.
1. **Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Yereller penceresindeki `currentCount` alanın değerini gözden geçirin:

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

Blazor Webassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:

1. İçindeki `OnInitializedAsync` *fetchdata. Razor* sayfasında bir kesme noktası ayarlayın.
1. `Get` Eylem yönteminde içinde `WeatherForecastController` bir kesme noktası ayarlayın.
1. Bir HTTP isteğini sunucuya vermeden önce, `FetchData` bileşendeki ilk kesme noktasına gitmek Için **verileri getir** sekmesine gidin:

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController`:

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code bir Blazor webassembly uygulamasında hata ayıklamak için:
 
1. [C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını olarak `debug.javascript.usePreview` ayarlandığı şekilde `true`yükler.

   ![Uzantıları](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Hata ayıklama etkinken Blazor mevcut bir webassembly uygulamasını açın.

   * Hata ayıklamayı etkinleştirmek için ek kurulum gerekli olduğunda aşağıdaki bildirime sahipseniz, doğru uzantıların yüklü olduğunu ve JavaScript önizlemesi hata ayıklamanın etkin olduğunu doğrulayın ve ardından pencereyi yeniden yükleyin:

     ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Derleme ve hata ayıklama için gerekli varlıkları uygulamaya eklemek için bir bildirim sunulur. **Evet**' i seçin:

     ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Uygulamanın hata ayıklayıcıda başlatılması iki adımlı bir işlemdir:

   1 \. **İlk**olarak, **.NET Core başlatma (Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.

   2 \. **Uygulama başladıktan sonra**, Chrome başlatma yapılandırmasındaki **.NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir). Chrome yerine Edge kullanmak için, *. vscode/Launch. JSON* içindeki başlatma yapılandırmasının öğesini `pwa-chrome` olarak `pwa-msedge`değiştirin. `type`

1. `Counter` Bileşendeki `IncrementCount` yöntemde bir kesme noktası ayarlayın ve ardından kesme noktasına isabet eden düğmeyi seçin:

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıkla

1. Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.

1. <kbd>SHIFT</kbd>+<kbd>Alt</kbd>alt+<kbd>D</kbd>tuşuna basın.

1. Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır. Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur. Hata sayfası, hata ayıklama proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken Blazor tarayıcıyı çalıştırmaya yönelik yönergeler içerir. *Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.

Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir. Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs*/*. Razor* kaynak dosyalarını bulun. Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir. Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.

Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler. Klavye kısayoluna hata ayıklama basıldığında, ara Blazor sunucu üzerindeki Chrome DevTools ' ı işaret eder. Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).

## <a name="browser-source-maps"></a>Tarayıcı kaynağı eşlemeleri

Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir. Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez. Bunun yerine Blazor , tarayıcı içinde Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.

## <a name="troubleshoot"></a>Sorun giderme

Hatalar halinde çalıştırıyorsanız, aşağıdaki ipucu yardımcı olabilir:

**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın. Konsolunda, tüm kesme noktalarını `localStorage.clear()` kaldırmak için yürütün.
