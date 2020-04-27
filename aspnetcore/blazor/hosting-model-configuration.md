---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: Razor bileşenlerini Blazor Razor Pages ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159625"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor barındırma modeli yapılandırması

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu makalede barındırma modeli yapılandırması ele alınmaktadır.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>Ortam

Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır. Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.

Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan ortamı seçer. Üstbilginin değeri ortamıdır. Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır. Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments>..

Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, geliştirme `blazor-environment` ortamını belirtmek için üst bilgi ekler. Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.

IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin. *Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:

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
> Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *Web. config* dosyasını kullanmak için, bkz..

Ekleme `IWebAssemblyHostEnvironment` tarafından bir bileşende uygulamanın ortamını edinin ve `Environment` özelliği okuyun:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Başlangıç sırasında, geliştiricilerin `WebAssemblyHostBuilder` kendi kodlarında `IWebAssemblyHostEnvironment` ortama özgü `HostEnvironment` mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* ' Isenvironment ("{ortam adı}")

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

Özelliği `IWebAssemblyHostEnvironment.BaseAddress` , `NavigationManager` hizmet kullanılamadığında başlatma sırasında kullanılabilir.

### <a name="configuration"></a>Yapılandırma

Blazor WebAssembly yapılandırmayı şunları destekler:

* Uygulama ayarları dosyaları için varsayılan olarak [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) :
  * *Wwwroot/appSettings. JSON*
  * *Wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .

> [!WARNING]
> Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir. **Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**

Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index>..

#### <a name="app-settings-configuration"></a>Uygulama ayarları yapılandırması

*Wwwroot/appSettings. JSON*:

```json
{
  "message": "Hello from config!"
}
```

Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Sağlayıcı yapılandırması

Aşağıdaki örnek, ek yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> sağlamak için bir ve [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) kullanır:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a>Kimlik doğrulama yapılandırması

*Wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Günlüğe kaydetme yapılandırması

*Wwwroot/appSettings. JSON*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Konak Oluşturucu yapılandırması

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Önbelleğe alınmış yapılandırma

Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır. [Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz. Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:

* Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.
* PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.

Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates>..

### <a name="logging"></a>Günlüğe kaydetme

Blazor WebAssembly günlük desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Kullanıcı arabirimindeki bağlantı durumunu yansıtır

İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir. Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.

Kullanıcı arabirimini özelleştirmek için, *_Host. cshtml* Razor sayfasında `id` öğesinin `components-reconnect-modal` içeren `<body>` bir öğesi tanımlayın:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Aşağıdaki tabloda `components-reconnect-modal` ÖĞESINE uygulanan CSS sınıfları açıklanmaktadır.

| CSS sınıfı                       | Bildiren&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Kayıp bir bağlantı. İstemci yeniden bağlanmaya çalışıyor. Kalıcı olarak göster. |
| `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur. Kalıcı olarak gizleyin. |
| `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu. Yeniden bağlanmayı denemek için çağrısı `window.Blazor.reconnect()`yapın. |
| `components-reconnect-rejected` | Yeniden bağlantı reddedildi. Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu. Uygulamayı yeniden yüklemek için çağrısı `location.reload()`yapın. Bu bağlantı durumu şu durumlarda oluşabilir:<ul><li>Sunucu tarafında devre dışı bir kilitlenme oluşur.</li><li>Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil. Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</li><li>Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</li></ul> |

### <a name="render-mode"></a>Oluşturma modu

Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar. Bu, *_Host. cshtml* Razor sayfasında ayarlanır:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.

| `RenderMode`        | Açıklama |
| ------------------- | ----------- |
| `ServerPrerendered` | Bileşeni statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır. |
| `Server`            | Blazor Sunucu uygulaması için bir işaret oluşturur. Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır. |
| `Static`            | Bileşeni statik HTML olarak işler. |

Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma

Bazen, sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir. Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.

İstemciyi, SignalR *Pages/_Host. cshtml* dosyasında yapılandırmak için:

* Betiğin etiketine bir `autostart="false"` `<script>` `blazor.server.js` ekleyin.
* Oluşturucuyu belirten bir yapılandırma nesnesi çağırın `Blazor.start` ve geçirin. SignalR

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

### <a name="logging"></a>Günlüğe kaydetme

Sunucu günlüğü desteği Blazor hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..
