---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: Blazor Razor Bileşenleri Razor Sayfalar ve MVC uygulamalarıyla tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 3cef67806ce0e2e045122bdc962e93795be68572
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679585"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor barındırma modeli yapılandırması

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu makalede barındırma modeli yapılandırması ele alınmaktadır.

## <a name="blazor-webassembly"></a>BlazorWebAssembly

### <a name="environment"></a>Ortam

Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır. Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.

Barındırılan Blazor webassembly uygulaması, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan alır `blazor-environment` . Üstbilginin değeri ortamıdır. Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır. Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..

Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler. Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.

IIS için aşağıdaki örnekte, yayımlanan *web.config* dosyasına özel üst bilgiyi ekleyin. *web.config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:

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
> Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel bir *web.config* dosyası kullanmak için, bkz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> ..

Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:

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
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .

### <a name="configuration"></a>Yapılandırma

BlazorWebAssembly yapılandırmayı şuradan yükler:

* Uygulama ayarları dosyaları varsayılan olarak:
  * *Wwwroot/appsettings.jsaçık*
  * *Wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) . Tüm sağlayıcılar Blazor webassembly uygulamalarına uygun değildir. Wezsembly için desteklenen sağlayıcıların açıklanması Blazor , [ISG için yapılandırma sağlayıcıları Blazor (DotNet/AspNetCore.Docs #18134) açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.

> [!WARNING]
> BlazorWebassembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir. **Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**

Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..

#### <a name="app-settings-configuration"></a>Uygulama ayarları yapılandırması

*Wwwroot/appsettings.js*:

```json
{
  "message": "Hello from config!"
}
```

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Sağlayıcı yapılandırması

Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

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

builder.Configuration.Add(memoryConfig);
```

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak üzere bir kullanın. Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:

*Wwwroot/cars.js*:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>Kimlik doğrulama yapılandırması

*Wwwroot/appsettings.js*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a>Günlüğe kaydetme yapılandırması

[Microsoft.Extensions.Logging.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)için bir paket başvurusu ekleyin:

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

*Wwwroot/appsettings.js*:

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
using Microsoft.Extensions.Logging;

...

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
* PWA 'nın *service-worker.js* ve *service-worker-assets.js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi üzerinde uygulamaya işaret eden, uygulamanın yeniden dağıtıldığını belirten bir daha ziyaret etmelidir.

Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..

### <a name="logging"></a>Günlüğe Kaydetme

BlazorWebassembly günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRkimlik doğrulaması için çıkış noktaları arası anlaşma

SignalRTemel alınan istemciyi tanımlama bilgileri veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Çıkış noktaları arası [getirme](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) isteklerini ayarlamak için kullanın:

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Seçeneğini seçeneğe atayın:

  ```csharp
  var client = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.

## <a name="blazor-server"></a>BlazorServer

### <a name="reflect-the-connection-state-in-the-ui"></a>Kullanıcı arabirimindeki bağlantı durumunu yansıtır

İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir. Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.

Kullanıcı arabirimini özelleştirmek için, `id` `components-reconnect-modal` `<body>` *_Host. cshtml* sayfasında öğesinin içeren bir öğesi tanımlayın Razor :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .

| CSS sınıfı                       | Bildiren&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Kayıp bir bağlantı. İstemci yeniden bağlanmaya çalışıyor. Kalıcı olarak göster. |
| `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur. Kalıcı olarak gizleyin. |
| `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu. Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Yeniden bağlantı reddedildi. Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu. Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` . Bu bağlantı durumu şu durumlarda oluşabilir:<ul><li>Sunucu tarafında devre dışı bir kilitlenme oluşur.</li><li>Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil. Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</li><li>Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</li></ul> |

### <a name="render-mode"></a>Oluşturma modu

BlazorSunucu uygulamaları, sunucu bağlantısı kurumadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar. Bu, *_Host. cshtml* Razor sayfasında ayarlanır:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Sunucu uygulaması için bir işaret oluşturur Blazor . Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Bileşeni statik HTML olarak işler. |

Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>SignalRSunucu uygulamaları için istemciyi Blazor yapılandırma

Bazen, SignalR sunucu uygulamaları tarafından kullanılan istemciyi yapılandırmanız gerekir Blazor . Örneğin, SignalR bir bağlantı sorununu tanılamak için istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.

SignalRİstemciyi, *Pages/_Host. cshtml* dosyasında yapılandırmak için:

* `autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .
* `Blazor.start`Oluşturucuyu belirten bir yapılandırma nesnesi çağırın ve geçirin SignalR .

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

### <a name="logging"></a>Günlüğe Kaydetme

BlazorSunucu günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..
