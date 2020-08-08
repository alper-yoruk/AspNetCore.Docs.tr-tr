---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: ASP.NET Core barındırma modeli yapılandırmasına yönelik ek senaryolar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
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
ms.openlocfilehash: dbad91e46a95d9ab5ec62d66e0d9a18938ff4520
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014470"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="da660-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="da660-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="da660-104">[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="da660-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="da660-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="da660-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="da660-106">SignalRkimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="da660-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="da660-107">*Bu bölüm için geçerlidir Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="da660-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="da660-108">SignalRTemel alınan istemciyi, cookie s veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="da660-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="da660-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Kaynak noktaları arası isteklere ayarlamak için kullanın [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="da660-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="da660-110"><xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Seçeneğini seçeneğe atayın:</span><span class="sxs-lookup"><span data-stu-id="da660-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="da660-111">Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="da660-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="da660-112">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="da660-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="da660-113">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="da660-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="da660-114">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="da660-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="da660-115">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="da660-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="da660-116">Kullanıcı arabirimini özelleştirmek için, sayfanın içindeki öğesiyle bir öğe `id` tanımlayın `components-reconnect-modal` `<body>` `_Host.cshtml` Razor :</span><span class="sxs-lookup"><span data-stu-id="da660-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="da660-117">Uygulamanın stil sayfasına aşağıdakini ekleyin ( `wwwroot/css/app.css` veya `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="da660-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="da660-118">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="da660-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="da660-119">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="da660-119">CSS class</span></span>                       | <span data-ttu-id="da660-120">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="da660-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="da660-121">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="da660-121">A lost connection.</span></span> <span data-ttu-id="da660-122">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="da660-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="da660-123">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="da660-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="da660-124">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="da660-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="da660-125">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="da660-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="da660-126">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="da660-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="da660-127">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="da660-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="da660-128">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="da660-128">Reconnection rejected.</span></span> <span data-ttu-id="da660-129">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="da660-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="da660-130">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="da660-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="da660-131">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="da660-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="da660-132">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="da660-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="da660-133">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="da660-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="da660-134">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="da660-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="da660-135">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="da660-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="da660-136">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="da660-136">Render mode</span></span>

<span data-ttu-id="da660-137">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="da660-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="da660-138">Blazor Serveruygulamalar, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="da660-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="da660-139">Bu, `_Host.cshtml` Razor sayfada ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="da660-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="da660-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="da660-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="da660-141">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="da660-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="da660-142">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="da660-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="da660-143">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="da660-143">Render mode</span></span> | <span data-ttu-id="da660-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="da660-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="da660-145">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="da660-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="da660-146">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="da660-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="da660-147">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="da660-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="da660-148">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="da660-148">Output from the component isn't included.</span></span> <span data-ttu-id="da660-149">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="da660-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="da660-150">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="da660-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="da660-151">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="da660-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="da660-152">SignalRUygulamalar için istemciyi yapılandırma Blazor Server</span><span class="sxs-lookup"><span data-stu-id="da660-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="da660-153">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="da660-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="da660-154">SignalRDosyadaki uygulamalar tarafından kullanılan istemciyi yapılandırın Blazor Server `Pages/_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="da660-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="da660-155">`Blazor.start` `_framework/blazor.server.js` Komut dosyasını ve etiketinin arkasına çağrı yapan bir betik yerleştirin `</body>` .</span><span class="sxs-lookup"><span data-stu-id="da660-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="da660-156">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="da660-156">Logging</span></span>

<span data-ttu-id="da660-157">SignalRİstemci günlüğünü yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="da660-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="da660-158">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="da660-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="da660-159">`configureSignalR` `configureLogging` İstemci Oluşturucu üzerinde günlük düzeyiyle çağıran bir yapılandırma nesnesi () geçirin.</span><span class="sxs-lookup"><span data-stu-id="da660-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

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

<span data-ttu-id="da660-160">Yukarıdaki örnekte, `information` günlük düzeyine eşdeğerdir <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="da660-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="da660-161">Yeniden bağlanma işleyicisini değiştirme</span><span class="sxs-lookup"><span data-stu-id="da660-161">Modify the reconnection handler</span></span>

<span data-ttu-id="da660-162">Yeniden bağlantı işleyicisinin devre bağlantı olayları, şu gibi özel davranışlar için değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="da660-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="da660-163">Bağlantı kesildiğinde kullanıcıya bildirimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="da660-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="da660-164">Bir devre bağlıyken günlüğe kaydetme (istemciden) gerçekleştirmek için.</span><span class="sxs-lookup"><span data-stu-id="da660-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="da660-165">Bağlantı olaylarını değiştirmek için:</span><span class="sxs-lookup"><span data-stu-id="da660-165">To modify the connection events:</span></span>

* <span data-ttu-id="da660-166">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="da660-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="da660-167">Bırakılan bağlantılar ( `onConnectionDown` ) ve kurulan/yeniden kurulan bağlantılar () için bağlantı değişiklikleri için geri çağırmaları kaydedin `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="da660-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="da660-168">**Her ikisi** `onConnectionDown` ve `onConnectionUp` belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="da660-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="da660-169">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlama</span><span class="sxs-lookup"><span data-stu-id="da660-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="da660-170">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlamak için:</span><span class="sxs-lookup"><span data-stu-id="da660-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="da660-171">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="da660-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="da660-172">`maxRetries`Her yeniden deneme girişimi için izin verilen yeniden deneme sayısını () ve süreyi milisaniye cinsinden ayarlayın ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="da660-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="da660-173">Yeniden bağlantı görüntüsünü gizleme veya değiştirme</span><span class="sxs-lookup"><span data-stu-id="da660-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="da660-174">Yeniden bağlantı görüntüsünü gizlemek için:</span><span class="sxs-lookup"><span data-stu-id="da660-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="da660-175">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="da660-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="da660-176">Yeniden bağlantı işleyicisini `_reconnectionDisplay` boş bir nesneye ayarlayın ( `{}` veya `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="da660-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

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

<span data-ttu-id="da660-177">Yeniden bağlantı görüntüsünü değiştirmek için, `_reconnectionDisplay` Önceki örnekte görüntülenecek öğe olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="da660-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="da660-178">Yer tutucu, `{ELEMENT ID}` görüntülenecek HTML ÖĞESININ kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="da660-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="da660-179">HTML `<head>` etiketi öğelerini etkiler</span><span class="sxs-lookup"><span data-stu-id="da660-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="da660-180">*Bu bölüm, ve ' nin yakında ASP.NET Core 5,0 sürümü için geçerlidir Blazor WebAssembly Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="da660-180">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="da660-181">İşlendiğinde,, `Title` `Link` ve `Meta` bileşenleri HTML etiketi öğelerine veri ekler veya güncelleştirir `<head>` :</span><span class="sxs-lookup"><span data-stu-id="da660-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="da660-182">Önceki örnekte,,, ve için yer tutucular `{TITLE}` `{URL}` `{DESCRIPTION}` dize değerleri, Razor değişkenler veya Razor ifadeleridir.</span><span class="sxs-lookup"><span data-stu-id="da660-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="da660-183">Aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="da660-183">The following characteristics apply:</span></span>

* <span data-ttu-id="da660-184">Sunucu tarafı prerendering desteklenir.</span><span class="sxs-lookup"><span data-stu-id="da660-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="da660-185">`Value`Parametresi, bileşen için tek geçerli parametredir `Title` .</span><span class="sxs-lookup"><span data-stu-id="da660-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="da660-186">Ve bileşenlerine verilen HTML öznitelikleri `Meta` , `Link` [ek özniteliklerde](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) yakalanıp işlenen HTML etiketine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="da660-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="da660-187">Birden çok `Title` bileşen için, sayfanın başlığı, `Value` işlenen son bileşenin sayısını yansıtır `Title` .</span><span class="sxs-lookup"><span data-stu-id="da660-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="da660-188">`Meta` `Link` Aynı özniteliklere sahip birden çok veya bileşen varsa, veya bileşen başına tam olarak bir HTML etiketi işlenir `Meta` `Link` .</span><span class="sxs-lookup"><span data-stu-id="da660-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="da660-189">İki `Meta` veya `Link` bileşen aynı işlenen HTML etiketine başvuramaz.</span><span class="sxs-lookup"><span data-stu-id="da660-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="da660-190">Mevcut veya bileşenlerin parametrelerine yapılan değişiklikler `Meta` , `Link` İşlenmiş HTML etiketlerine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="da660-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="da660-191">`Link`Veya `Meta` bileşenleri artık oluşturulmuyorsa ve bu nedenle Framework tarafından atılmışsa, işlenen HTML etiketleri kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="da660-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="da660-192">Bir alt bileşende çerçeve bileşenlerinden biri kullanıldığında, çerçeve bileşenini içeren alt bileşen işlendiği sürece işlenen HTML etiketi, ana bileşenin diğer alt bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="da660-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="da660-193">Bir alt bileşendeki bu çerçeve bileşenlerinden birini kullanma ve veya ' a bir HTML etiketi yerleştirme arasındaki ayrım, `wwwroot/index.html` `Pages/_Host.cshtml` bir çerçeve BILEŞENININ işlenmiş html etiketinin:</span><span class="sxs-lookup"><span data-stu-id="da660-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="da660-194">, Uygulama durumu ile değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="da660-194">Can be modified by application state.</span></span> <span data-ttu-id="da660-195">Sabit kodlanmış HTML etiketi uygulama durumu tarafından değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="da660-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="da660-196">`<head>`Üst bileşen artık IŞLENMEDIĞINDE HTML 'den kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="da660-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da660-197">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="da660-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
