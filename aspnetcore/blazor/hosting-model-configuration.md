---
title: ASP.NET Blazor Core hosting modeli yapılandırma
author: guardrex
description: Razor Blazor bileşenlerinin Razor Pages ve MVC uygulamalarına nasıl entegre edilebildiğini de içeren model yapılandırmasını barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 6a3731657d11faed0b005b429058343b2be4c44b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791473"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor hosting modeli yapılandırma

Yazar: [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu makalede, barındırma modeli yapılandırmakapsar.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>Ortam

Bir uygulamayı yerel olarak çalıştırırken, ortam Geliştirme varsayılan olarak Uygulama yayımlandığında, ortam Üretim varsayılandır.

Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üstbilgi ekleyerek ortamı tarayıcıya bildiren bir ara yazılım aracılığıyla ortamı sunucudan alır. Üstbilginin değeri ortamdır. Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır. Ortamın nasıl yapılandırılabildiğini de içeren <xref:fundamentals/environments>daha fazla bilgi için bkz.

Yerel olarak çalışan bağımsız bir uygulama için `blazor-environment` geliştirme sunucusu Geliştirme ortamını belirtmek için üstbilgi ekler. Diğer barındırma ortamları için ortamı `blazor-environment` belirtmek için üstbilgi ekleyin.

IIS için aşağıdaki örnekte, yayınlanan *web.config* dosyasına özel üstbilgi ekleyin. *web.config* dosyası *depo/Yayın/{TARGET FRAMEWORK}/publish* klasöründe bulunur:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Uygulama *yayımlama* klasöründe yayımlandığında üzerine yazılmayan IIS için özel bir <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *web.config* dosyası kullanmak için bkz.

Özelliği enjekte ederek ve okuyarak uygulamanın ortamını bir bileşende elde edin: `IWebAssemblyHostEnvironment` `Environment`

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Başlangıç sırasında, `WebAssemblyHostBuilder` geliştiricilerin `IWebAssemblyHostEnvironment` kodlarında ortama özgü bir mantığa sahip olmalarını sağlayan özellik aracılığıyla `HostEnvironment` ortaya çıkar:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Aşağıdaki kolaylık uzatma yöntemleri, Geliştirme, Üretim, Evreleme ve özel ortam adları için geçerli ortamın denetlenmesine izin verir:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* 'IsEnvironment("{ENVIRONMENT NAME}")

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

Özellik, `IWebAssemblyHostEnvironment.BaseAddress` `NavigationManager` hizmet kullanılamadığında başlangıç sırasında kullanılabilir.

### <a name="configuration"></a>Yapılandırma

ASP.NET Core 3.2 Preview 3 sürümü itibariyle[(geçerli sürüm 3.2 Önizleme 4),](xref:blazor/get-started)Blazor WebAssembly yapılandırma destekler:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings adresinde n için. {ÇEVRE}.json*

*wwwroot* klasörüne bir *appsettings.json* dosyası ekleyin:

```json
{
  "message": "Hello from config!"
}
```

Yapılandırma <xref:Microsoft.Extensions.Configuration.IConfiguration> verilerine erişmek için bir bileşene örnek enjekte edin:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir. **Uygulama sırlarını veya kimlik bilgilerini yapılandırmada saklamayın.**

Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır. [Aşamalı Web Uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile yapılandırma dosyalarını yalnızca yeni bir dağıtım oluştururken güncelleştirebilirsiniz. Dağıtımlar arasında yapılandırma dosyalarını düzenlemenin hiçbir etkisi yoktur, çünkü:

* Kullanıcılar, kullanmaya devam ettikleri dosyaların sürümlerini önbelleğe aldılar.
* PWA'nın *hizmet-worker.js* ve *hizmet-işçi-varlıklar.js* dosyaları derleme üzerine yeniden oluşturulmalıdır, bu da kullanıcının bir sonraki çevrimiçi ziyaretinde uygulamaya uygulamanın yeniden dağıtıldığını bildiren bir sinyal verir.

Arka plan güncelleştirmelerinin PWA'lar tarafından <xref:blazor/progressive-web-app#background-updates>nasıl işlendiği hakkında daha fazla bilgi için bkz.

### <a name="logging"></a>Günlüğe Kaydetme

Blazor WebAssembly günlük desteği hakkında <xref:fundamentals/logging/index#create-logs-in-blazor-webassembly>bilgi için bkz.

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Bağlantı durumunu UI'de yansıtma

İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmaya çalışırken varsayılan kullanıcı arabirimi kullanıcıya görüntülenir. Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.

UI'yi özelleştirmek `id` için, *_Host.cshtml* Razor sayfasındaki öğeyi içeren bir `components-reconnect-modal` `<body>` öğe tanımlayın:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Aşağıdaki tabloda `components-reconnect-modal` öğeye uygulanan CSS sınıfları açıklanmaktadır.

| CSS sınıfı                       | Gösterir&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Kayıp bir bağlantı. İstemci yeniden bağlanmaya çalışıyor. Modal'ı göster. |
| `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden kurulur. Modal'ı sakla. |
| `components-reconnect-failed`   | Yeniden bağlantı, büyük olasılıkla bir ağ hatası nedeniyle başarısız oldu. Yeniden bağlanmayı denemek `window.Blazor.reconnect()`için. |
| `components-reconnect-rejected` | Yeniden bağlantı reddedildi. Sunucuya ulaşıldı ancak bağlantıyı reddetti ve kullanıcının sunucudaki durumu kaybolur. Uygulamayı yeniden yüklemek için `location.reload()`. Bu bağlantı durumu şu anda neden olabilir:<ul><li>Sunucu tarafındaki devrede bir kilitlenme oluşur.</li><li>İstemci, sunucunun kullanıcının durumunu bırakabilecek kadar uzun süre bağlantısı kesildi. Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri bertaraf edilir.</li><li>Sunucu yeniden başlatılır veya uygulamanın alt işlemi geri dönüştürülür.</li></ul> |

### <a name="render-mode"></a>Render modu

Blazor Server uygulamaları, sunucuya istemci bağlantısı kurulmadan önce ui'yi sunucuda önceden işlemek için varsayılan olarak ayarlanır. Bu *_Host.cshtml* Razor sayfasında ayarlanır:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`bileşenin:

* Sayfaya önceden işlenir.
* Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.

| `RenderMode`        | Açıklama |
| ------------------- | ----------- |
| `ServerPrerendered` | Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir. Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır. |
| `Server`            | Blazor Sunucu uygulaması için işaretleyici işler. Bileşenden gelen çıktı dahil değildir. Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır. |
| `Static`            | Bileşeni statik HTML'ye dönüştürür. |

Sunucu bileşenlerinin statik bir HTML sayfasından görüntülenmeleri desteklenmez.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor sayfalarından ve görünümlerinden durumlu etkileşimli bileşenler oluşturma

Bir Razor sayfasına veya görünümüne devletli etkileşimli bileşenler eklenebilir.

Sayfa veya görünüm işlendiğinde:

* Bileşen sayfa veya görünüm le önceden işlenmiştir.
* Ön işleme için kullanılan ilk bileşen durumu kaybolur.
* SignalR Bağlantı kurulduğunda yeni bileşen durumu oluşturulur.

Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor sayfalarından ve görünümlerinden etkileşimli olmayan bileşenler oluşturma

Aşağıdaki Razor sayfasında bileşen, `Counter` bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` Statik olarak işlendiğiiçin bileşen etkileşimli olamaz.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma

Bazen, Sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir. Örneğin, bağlantı sorunu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.

SignalR *İstemciyi Pages/_Host.cshtml* dosyasında yapılandırmak için:

* Komut `autostart="false"` dosyası için `<script>` etikete bir öznitelik ekleyin. `blazor.server.js`
* Oluşturucuyu belirten bir yapılandırma `Blazor.start` nesnesinde SignalR arama ve geçiş.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
