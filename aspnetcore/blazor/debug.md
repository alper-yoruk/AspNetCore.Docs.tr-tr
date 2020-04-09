---
title: Hata Ayıklama Blazor ASP.NET Çekirdek WebAssembly
author: guardrex
description: Uygulamaları nasıl hata Blazor ayıklamakonusunda öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: eaa67d63f6d15249885d78d3de197ae53e73f072
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80381868"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Hata Ayıklama Blazor ASP.NET Çekirdek WebAssembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly uygulamaları Krom tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak debugged olabilir.  Alternatif olarak Visual Studio veya Visual Studio Code'u kullanarak uygulamanızı hata ayıklayabilirsiniz.

Kullanılabilir senaryolar şunlardır:

* Kesme noktalarını ayarlayın ve kaldırın.
* Visual Studio ve Visual Studio Code<kbd>(F5</kbd> desteği) hata ayıklama desteği ile uygulamayı çalıştırın.
* Kod aracılığıyla tek adımlı<kbd>(F10).</kbd>
* Visual Studio veya Visual Studio Code'da bir tarayıcıda <kbd>F8</kbd> veya <kbd>F5</kbd> ile kod yürütmeye devam edin.
* Yerel *ekranda,* yerel değişkenlerin değerlerini gözlemleyin.
* JavaScript'ten .NET'e ve .NET'ten JavaScript'e giden çağrı zincirleri de dahil olmak üzere arama yığınına bakın.

Şimdilik, *yapamam:*

* Dizileri inceleyin.
* Üyeleri incelemek için hover.
* Yönetilen koda hata ayıklama adımı veya çıkan.
* Değer türlerini incelemek için tam desteğe sahip.
* Işlenmemiş özel durumlara son verin.
* Uygulama nın başlatılması sırasında kesme noktalarına vurun.
* Bir servis çalışanı olan bir uygulamayı hata ayıklama.

Biz yaklaşan sürümlerde hata ayıklama deneyimini geliştirmeye devam edecektir.

## <a name="prerequisites"></a>Ön koşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Microsoft Edge (sürüm 80 veya sonraki sürüm)
* Google Chrome (sürüm 70 veya sonrası)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio ve Visual Studio Code için hata ayıklama etkinleştirme

Hata ayıklama, Core 3.2 Preview 3 veya daha sonraki Blazor WebAssembly proje şablonu ASP.NET kullanılarak oluşturulan yeni projeler için otomatik olarak etkinleştirilir.

Varolan Blazor bir WebAssembly uygulaması için hata ayıklamayı etkinleştirmek için, başlangıç projesindeki `inspectUri` *launchSettings.json* dosyasını her başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Bir kez güncelleştirildikten sonra *launchSettings.json* dosyası aşağıdaki örneğe benzer olmalıdır:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Özellik: `inspectUri`

* IDE'nin uygulamanın bir Blazor WebAssembly uygulaması olduğunu algılamasını sağlar.
* Komut dosyası hata ayıklama altyapısının 'hata Blazorayıklama proxy' aracılığıyla tarayıcıya bağlanmasını bildirir.

## <a name="visual-studio"></a>Visual Studio

Visual Studio'da bir Blazor WebAssembly uygulamasını hata ayıklamak için:

1. [Visual Studio 2019 16.6'nın (Önizleme](https://visualstudio.com/preview) 2 veya sonraki sürüm) en son önizleme sürümüne sahip olduğunuzdan emin olun.
1. Core barındırılan Blazor yeni bir ASP.NET WebAssembly uygulaması oluşturun.
1. Uygulamayı hata ayıklamada çalıştırmak için <kbd>F5</kbd> tuşuna basın.
1. `IncrementCount` Yöntemde *Counter.razor'da* bir kırılma noktası ayarlayın.
1. **Sayaç** sekmesine göz atın ve kesme noktasına basmak için düğmeyi seçin:

   ![Hata Ayıklama Sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Yerel penceredeki `currentCount` alanın değerine göz atın:

   ![Yerel halkları görüntüleyin](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

WebAssembly uygulamanızı Blazor hata ayıklarken sunucu kodunuzu da hata ayıklayabilirsiniz:

1. *FetchData.razor* sayfasında bir kesme noktası `OnInitializedAsync`ayarlayın.
1. `Get` Eylem yönteminde `WeatherForecastController` bir kesme noktası ayarlayın.
1. Sunucuya bir **Fetch Data** HTTP isteği vermeden hemen önce `FetchData` bileşendeki ilk kesme noktasına basmak için Verileri Getir sekmesine göz atın:

   ![Hata Ayıklama Verisi](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın `WeatherForecastController`ve ardından sunucudaki kesme noktasına şu şekilde vurdu:

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Yürütmenin devam etmesini ve hava tahmini tablosunun işlenmesini görmek için <kbd>F5</kbd> tuşuna tekrar basın.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Blazor Code'ta bir WebAssembly uygulamasını hata ayıklamak için:
 
1. [C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript Hata Ayıklama (Gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` ' olarak `true`ayarlayın.

   ![Uzantıları](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Hata ayıklama etkin olan varolan Blazor bir WebAssembly uygulamasını açın.

   * Hata ayıklamayı etkinleştirmek için ek kurulum gerektiğine dair aşağıdaki bildirimi alırsanız, doğru uzantıların yüklü olduğunu ve JavaScript önizleme hata ayıklamanın etkin olduğunu onaylayın ve ardından pencereyi yeniden yükleyin:

     ![Ek kurulum requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Bir bildirim, bina ve hata ayıklama için gerekli varlıkları uygulamaya eklemeyi teklif ediyor. **Evet'i**seçin :

     ![Gerekli varlıkları ekleme](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Uygulamayı hata ayıklamada başlatmak iki aşamalı bir işlemdir:

   1\. **İlk olarak**, **.NET Core LaunchBlazor (Bağımsız)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.

   2\. **Uygulama başladıktan sonra**Chrome başlatma **yapılandırmasında .NET Blazor Core Hata Ayıklama Web Derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerektirir). Chrome yerine Edge'i kullanmak `type` için *.vscode/launch.json'daki* `pwa-chrome` başlatma `pwa-msedge`yapılandırmasını .

1. `Counter` Bileşendeki `IncrementCount` yöntemde bir kesme noktası ayarlayın ve ardından kesme noktasına basmak için düğmeyi seçin:

   ![VS Kodunda Hata Ayıklama Sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıklama

1. Geliştirme ortamında uygulamanın hata ayıklama oluşturma sını çalıştırın.

1. +Shift <kbd>Shift</kbd><kbd>Alt</kbd>+<kbd>D</kbd>tuşuna basın.

1. Tarayıcı uzaktan hata ayıklama etkin çalıştırılmalıdır. Uzaktan hata ayıklama devre dışı bırakılırsa, **hata ayıklanabilir tarayıcı sekmesi** hata sayfası bulunamaz. Hata sayfası, hata ayıklama proxy'sinin uygulamaya bağlanabilmesi için Blazor hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmak için yönergeler içerir. *Tüm tarayıcı örneklerini kapatın* ve tarayıcıyı söylendiği gibi yeniden başlatın.

Tarayıcı uzaktan hata ayıklama etkinken çalıştırDıktan sonra, hata ayıklama klavyesi kısayolu yeni bir hata ayıklama sekmesini açar. Bir süre **sonra, Kaynaklar** sekmesi uygulamadaki .NET derlemelerinin listesini gösterir. Her derlemeyi genişletin ve hata ayıklama için kullanılabilir *.cs*/*.razor* kaynak dosyalarını bulun. Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktalarına vurulun. Bir kesme noktası vurulduktan sonra, tek adımlı<kbd>(F10)</kbd>kodu veya devam<kbd>(F8</kbd>) kod yürütme normal üzerinden.

Blazor[Chrome DevTools Protokolü'nü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolü ' yle artıran bir hata ayıklama proxy'si sağlar. NET'e özgü bilgiler. Klavye kısayolu hata ayıklama basıldığında, Blazor Chrome DevTools'u proxy'ye doğru işaret edin. Proxy hata ayıklamak istediğiniz tarayıcı penceresine bağlanır (dolayısıyla uzaktan hata ayıklama etkinleştirme gereksinimi).

## <a name="browser-source-maps"></a>Tarayıcı kaynak haritaları

Tarayıcı kaynak haritaları, tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına eşlemesine olanak sağlar ve genellikle istemci tarafı hata ayıklama için kullanılır. Ancak, Blazor şu anda C# ile doğrudan JavaScript/WASM eşlemiyor. Bunun Blazor yerine, tarayıcı içinde IL yorumu yapar, bu nedenle kaynak haritalar ilgili değildir.

## <a name="troubleshoot"></a>Sorun giderme

Hatalarla karşınıza çıktıysanız, aşağıdaki ipucu size yardımcı olabilir:

Hata **Ayıklama** sekmesinde, tarayıcınızdaki geliştirici araçlarını açın. Konsolda, herhangi `localStorage.clear()` bir kesme noktalarını kaldırmak için çalıştırın.
