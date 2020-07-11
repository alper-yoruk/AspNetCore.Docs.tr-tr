---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: ASP.NET Core barındırma modeli yapılandırmasına yönelik ek senaryolar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239640"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="b8b87-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b8b87-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b8b87-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="b8b87-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8b87-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b8b87-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="b8b87-106">kimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="b8b87-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="b8b87-107">*Bu bölüm için geçerlidir Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="b8b87-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="b8b87-108">SignalRTemel alınan istemciyi tanımlama bilgileri veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="b8b87-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="b8b87-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Kaynak noktaları arası isteklere ayarlamak için kullanın [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="b8b87-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="b8b87-110"><xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Seçeneğini seçeneğe atayın:</span><span class="sxs-lookup"><span data-stu-id="b8b87-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="b8b87-111">Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="b8b87-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b8b87-112">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="b8b87-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b8b87-113">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b8b87-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="b8b87-114">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b8b87-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b8b87-115">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b8b87-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b8b87-116">Kullanıcı arabirimini özelleştirmek için, sayfanın içindeki öğesiyle bir öğe `id` tanımlayın `components-reconnect-modal` `<body>` `_Host.cshtml` Razor :</span><span class="sxs-lookup"><span data-stu-id="b8b87-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b8b87-117">Uygulamanın stil sayfasına aşağıdakini ekleyin ( `wwwroot/css/app.css` veya `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="b8b87-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="b8b87-118">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b8b87-119">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="b8b87-119">CSS class</span></span>                       | <span data-ttu-id="b8b87-120">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="b8b87-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="b8b87-121">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="b8b87-121">A lost connection.</span></span> <span data-ttu-id="b8b87-122">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="b8b87-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="b8b87-123">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="b8b87-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="b8b87-124">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b8b87-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="b8b87-125">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="b8b87-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="b8b87-126">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="b8b87-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b8b87-127">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="b8b87-128">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="b8b87-128">Reconnection rejected.</span></span> <span data-ttu-id="b8b87-129">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="b8b87-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b8b87-130">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b8b87-131">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="b8b87-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="b8b87-132">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="b8b87-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b8b87-133">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="b8b87-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b8b87-134">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="b8b87-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b8b87-135">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="b8b87-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="b8b87-136">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="b8b87-136">Render mode</span></span>

<span data-ttu-id="b8b87-137">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b8b87-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="b8b87-138">uygulamalar, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8b87-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b8b87-139">Bu, `_Host.cshtml` Razor sayfada ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="b8b87-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b8b87-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="b8b87-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="b8b87-141">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b8b87-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="b8b87-142">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8b87-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="b8b87-143">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="b8b87-143">Render mode</span></span> | <span data-ttu-id="b8b87-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b8b87-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="b8b87-145">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b8b87-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b8b87-146">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8b87-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="b8b87-147">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b8b87-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b8b87-148">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="b8b87-148">Output from the component isn't included.</span></span> <span data-ttu-id="b8b87-149">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="b8b87-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="b8b87-150">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="b8b87-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b8b87-151">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b8b87-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="b8b87-152">SignalRUygulamalar için istemciyi yapılandırma Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b8b87-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b8b87-153">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="b8b87-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="b8b87-154">SignalRDosyadaki uygulamalar tarafından kullanılan istemciyi yapılandırın Blazor Server `Pages/_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="b8b87-155">`Blazor.start` `_framework/blazor.server.js` Komut dosyasını ve etiketinin arkasına çağrı yapan bir betik yerleştirin `</body>` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="b8b87-156">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="b8b87-156">Logging</span></span>

<span data-ttu-id="b8b87-157">SignalRİstemci günlüğünü yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="b8b87-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="b8b87-158">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b8b87-159">`configureSignalR` `configureLogging` İstemci Oluşturucu üzerinde günlük düzeyiyle çağıran bir yapılandırma nesnesi () geçirin.</span><span class="sxs-lookup"><span data-stu-id="b8b87-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="b8b87-160">Yukarıdaki örnekte, `information` günlük düzeyine eşdeğerdir <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b8b87-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="b8b87-161">Yeniden bağlanma işleyicisini değiştirme</span><span class="sxs-lookup"><span data-stu-id="b8b87-161">Modify the reconnection handler</span></span>

<span data-ttu-id="b8b87-162">Yeniden bağlantı işleyicisinin devre bağlantı olayları, şu gibi özel davranışlar için değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="b8b87-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="b8b87-163">Bağlantı kesildiğinde kullanıcıya bildirimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="b8b87-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="b8b87-164">Bir devre bağlıyken günlüğe kaydetme (istemciden) gerçekleştirmek için.</span><span class="sxs-lookup"><span data-stu-id="b8b87-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="b8b87-165">Bağlantı olaylarını değiştirmek için:</span><span class="sxs-lookup"><span data-stu-id="b8b87-165">To modify the connection events:</span></span>

* <span data-ttu-id="b8b87-166">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b8b87-167">Bırakılan bağlantılar ( `onConnectionDown` ) ve kurulan/yeniden kurulan bağlantılar () için bağlantı değişiklikleri için geri çağırmaları kaydedin `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="b8b87-168">**Her ikisi** `onConnectionDown` ve `onConnectionUp` belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b8b87-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="b8b87-169">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8b87-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="b8b87-170">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="b8b87-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="b8b87-171">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b8b87-172">`maxRetries`Her yeniden deneme girişimi için izin verilen yeniden deneme sayısını () ve süreyi milisaniye cinsinden ayarlayın ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="b8b87-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="b8b87-173">Yeniden bağlantı görüntüsünü gizleme veya değiştirme</span><span class="sxs-lookup"><span data-stu-id="b8b87-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="b8b87-174">Yeniden bağlantı görüntüsünü gizlemek için:</span><span class="sxs-lookup"><span data-stu-id="b8b87-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="b8b87-175">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="b8b87-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b8b87-176">Yeniden bağlantı işleyicisini `_reconnectionDisplay` boş bir nesneye ayarlayın ( `{}` veya `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="b8b87-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="b8b87-177">Yeniden bağlantı görüntüsünü değiştirmek için, `_reconnectionDisplay` Önceki örnekte görüntülenecek öğe olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="b8b87-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="b8b87-178">Yer tutucu, `{ELEMENT ID}` görüntülenecek HTML ÖĞESININ kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="b8b87-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8b87-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8b87-179">Additional resources</span></span>

* <xref:fundamentals/logging/index>
