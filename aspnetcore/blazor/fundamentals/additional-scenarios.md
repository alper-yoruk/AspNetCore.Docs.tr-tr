---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: ASP.NET Core barındırma modeli yapılandırmasına yönelik ek senaryolar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 618e451f5cb8a4e8eaf355d398fdeb80190cf559
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202717"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor barındırma modeli yapılandırması

[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)ve [Luke Latham](https://github.com/guardrex) tarafından

Bu makalede barındırma modeli yapılandırması ele alınmaktadır.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR kimlik doğrulaması için çıkış noktaları arası anlaşma

*Bu bölüm için geçerlidir Blazor WebAssembly .*

SignalRTemel alınan istemciyi, cookie s veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Kaynak noktaları arası isteklere ayarlamak için kullanın [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
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
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Kullanıcı arabirimindeki bağlantı durumunu yansıtır

*Bu bölüm için geçerlidir Blazor Server .*

İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir. Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.

Kullanıcı arabirimini özelleştirmek için, sayfanın içindeki öğesiyle bir öğe `id` tanımlayın `components-reconnect-modal` `<body>` `_Host.cshtml` Razor :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Uygulamanın stil sayfasına aşağıdakini ekleyin ( `wwwroot/css/app.css` veya `wwwroot/css/site.css` ):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .

| CSS sınıfı                       | Bildiren&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Kayıp bir bağlantı. İstemci yeniden bağlanmaya çalışıyor. Kalıcı olarak göster. |
| `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur. Kalıcı olarak gizleyin. |
| `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu. Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Yeniden bağlantı reddedildi. Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu. Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` . Bu bağlantı durumu şu durumlarda oluşabilir:<ul><li>Sunucu tarafında devre dışı bir kilitlenme oluşur.</li><li>Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil. Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</li><li>Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</li></ul> |

## <a name="render-mode"></a>Oluşturma modu

*Bu bölüm için geçerlidir Blazor Server .*

Blazor Server uygulamalar, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar. Bu, `_Host.cshtml` Razor sayfada ayarlanır:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

| Oluşturma modu | Açıklama |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Bir uygulama için işaretleyici işler Blazor Server . Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Bileşeni statik HTML olarak işler. |

Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a>SignalRUygulamalar için istemciyi yapılandırma Blazor Server

*Bu bölüm için geçerlidir Blazor Server .*

SignalRDosyadaki uygulamalar tarafından kullanılan istemciyi yapılandırın Blazor Server `Pages/_Host.cshtml` . `Blazor.start` `_framework/blazor.server.js` Komut dosyasını ve etiketinin arkasına çağrı yapan bir betik yerleştirin `</body>` .

### <a name="logging"></a>Günlüğe Kaydetme

SignalRİstemci günlüğünü yapılandırmak için:

* `autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .
* `configureSignalR` `configureLogging` İstemci Oluşturucu üzerinde günlük düzeyiyle çağıran bir yapılandırma nesnesi () geçirin.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

Yukarıdaki örnekte, `information` günlük düzeyine eşdeğerdir <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Yeniden bağlanma işleyicisini değiştirme

Yeniden bağlantı işleyicisinin devre bağlantı olayları, şu gibi özel davranışlar için değiştirilebilir:

* Bağlantı kesildiğinde kullanıcıya bildirimde bulunur.
* Bir devre bağlıyken günlüğe kaydetme (istemciden) gerçekleştirmek için.

Bağlantı olaylarını değiştirmek için:

* `autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .
* Bırakılan bağlantılar ( `onConnectionDown` ) ve kurulan/yeniden kurulan bağlantılar () için bağlantı değişiklikleri için geri çağırmaları kaydedin `onConnectionUp` . **Her ikisi** `onConnectionDown` ve `onConnectionUp` belirtilmelidir.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlama

Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlamak için:

* `autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .
* `maxRetries`Her yeniden deneme girişimi için izin verilen yeniden deneme sayısını () ve süreyi milisaniye cinsinden ayarlayın ( `retryIntervalMilliseconds` ).

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a>Yeniden bağlantı görüntüsünü gizleme veya değiştirme

Yeniden bağlantı görüntüsünü gizlemek için:

* `autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .
* Yeniden bağlantı işleyicisini `_reconnectionDisplay` boş bir nesneye ayarlayın ( `{}` veya `new Object()` ).

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

Yeniden bağlantı görüntüsünü değiştirmek için, `_reconnectionDisplay` Önceki örnekte görüntülenecek öğe olarak ayarlayın:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Yer tutucu, `{ELEMENT ID}` görüntülenecek HTML ÖĞESININ kimliğidir.

## <a name="influence-html-head-tag-elements"></a>HTML `<head>` etiketi öğelerini etkiler

*Bu bölüm, ve ' nin yakında ASP.NET Core 5,0 sürümü için geçerlidir Blazor WebAssembly Blazor Server .*

İşlendiğinde,, `Title` `Link` ve `Meta` bileşenleri HTML etiketi öğelerine veri ekler veya güncelleştirir `<head>` :

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

Önceki örnekte,,, ve için yer tutucular `{TITLE}` `{URL}` `{DESCRIPTION}` dize değerleri, Razor değişkenler veya Razor ifadeleridir.

Aşağıdaki özellikler geçerlidir:

* Sunucu tarafı prerendering desteklenir.
* `Value`Parametresi, bileşen için tek geçerli parametredir `Title` .
* Ve bileşenlerine verilen HTML öznitelikleri `Meta` , `Link` [ek özniteliklerde](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) yakalanıp işlenen HTML etiketine geçirilir.
* Birden çok `Title` bileşen için, sayfanın başlığı, `Value` işlenen son bileşenin sayısını yansıtır `Title` .
* `Meta` `Link` Aynı özniteliklere sahip birden çok veya bileşen varsa, veya bileşen başına tam olarak bir HTML etiketi işlenir `Meta` `Link` . İki `Meta` veya `Link` bileşen aynı işlenen HTML etiketine başvuramaz.
* Mevcut veya bileşenlerin parametrelerine yapılan değişiklikler `Meta` , `Link` İşlenmiş HTML etiketlerine yansıtılır.
* `Link`Veya `Meta` bileşenleri artık oluşturulmuyorsa ve bu nedenle Framework tarafından atılmışsa, işlenen HTML etiketleri kaldırılır.

Bir alt bileşende çerçeve bileşenlerinden biri kullanıldığında, çerçeve bileşenini içeren alt bileşen işlendiği sürece işlenen HTML etiketi, ana bileşenin diğer alt bileşenlerini etkiler. Bir alt bileşendeki bu çerçeve bileşenlerinden birini kullanma ve veya ' a bir HTML etiketi yerleştirme arasındaki ayrım, `wwwroot/index.html` `Pages/_Host.cshtml` bir çerçeve BILEŞENININ işlenmiş html etiketinin:

* , Uygulama durumu ile değiştirilebilir. Sabit kodlanmış HTML etiketi uygulama durumu tarafından değiştirilemez.
* `<head>`Üst bileşen artık IŞLENMEDIĞINDE HTML 'den kaldırılır.

## <a name="static-files"></a>Statik dosyalar

*Bu bölüm için geçerlidir Blazor Server .*

Bir veya diğer yapılandırma ile ek dosya eşlemeleri oluşturmak için <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> aşağıdaki yaklaşımlardan **birini** kullanın. Aşağıdaki örneklerde, `{EXTENSION}` yer tutucu dosya uzantısıdır ve `{CONTENT TYPE}` yer tutucu içerik türüdür.

* () İçindeki () [bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection) aracılığıyla seçenekleri `Startup.ConfigureServices` kullanarak yapılandırın `Startup.cs` <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Bu yaklaşım, hizmet vermek için kullanılan aynı dosya sağlayıcısını yapılandırdığından `blazor.server.js` , özel yapılandırmanızın hizmeti sunmaya engel olmadığından emin olun `blazor.server.js` . Örneğin, sağlayıcıyı ile yapılandırarak JavaScript dosyaları için eşlemeyi kaldırmayın `provider.Mappings.Remove(".js")` .

* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>() İçinde için iki çağrı kullanın `Startup.Configure` `Startup.cs` :
  * İle ilk çağrıda özel dosya sağlayıcısını yapılandırın <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * İkinci ara yazılım, `blazor.server.js` Framework tarafından sunulan varsayılan statik dosya yapılandırmasını kullanan hizmet verir Blazor .

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/index>
