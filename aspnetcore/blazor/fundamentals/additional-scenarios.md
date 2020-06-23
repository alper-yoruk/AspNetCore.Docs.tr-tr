---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: ASP.NET Core barındırma modeli yapılandırmasına yönelik ek senaryolar hakkında bilgi edinin Blazor .
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 2efc13d5d4ab91ffdf6c4c7021072a2b3f83153f
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242660"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="02ef6-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="02ef6-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="02ef6-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="02ef6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="02ef6-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="02ef6-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="02ef6-106">kimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="02ef6-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="02ef6-107">*Bu bölüm Blazor webassembly için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="02ef6-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="02ef6-108">SignalRTemel alınan istemciyi tanımlama bilgileri veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="02ef6-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="02ef6-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Kaynak noktaları arası isteklere ayarlamak için kullanın [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="02ef6-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="02ef6-110"><xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Seçeneğini seçeneğe atayın:</span><span class="sxs-lookup"><span data-stu-id="02ef6-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="02ef6-111">Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="02ef6-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="02ef6-112">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="02ef6-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="02ef6-113">*Bu bölüm sunucu için geçerlidir Blazor .*</span><span class="sxs-lookup"><span data-stu-id="02ef6-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="02ef6-114">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="02ef6-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="02ef6-115">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="02ef6-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="02ef6-116">Kullanıcı arabirimini özelleştirmek için, sayfanın içindeki öğesiyle bir öğe `id` tanımlayın `components-reconnect-modal` `<body>` `_Host.cshtml` Razor :</span><span class="sxs-lookup"><span data-stu-id="02ef6-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="02ef6-117">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="02ef6-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="02ef6-118">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="02ef6-118">CSS class</span></span>                       | <span data-ttu-id="02ef6-119">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="02ef6-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="02ef6-120">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="02ef6-120">A lost connection.</span></span> <span data-ttu-id="02ef6-121">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="02ef6-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="02ef6-122">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="02ef6-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="02ef6-123">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02ef6-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="02ef6-124">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="02ef6-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="02ef6-125">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="02ef6-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="02ef6-126">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="02ef6-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="02ef6-127">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="02ef6-127">Reconnection rejected.</span></span> <span data-ttu-id="02ef6-128">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="02ef6-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="02ef6-129">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="02ef6-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="02ef6-130">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="02ef6-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="02ef6-131">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="02ef6-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="02ef6-132">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="02ef6-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="02ef6-133">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="02ef6-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="02ef6-134">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="02ef6-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="02ef6-135">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="02ef6-135">Render mode</span></span>

<span data-ttu-id="02ef6-136">*Bu bölüm sunucu için geçerlidir Blazor .*</span><span class="sxs-lookup"><span data-stu-id="02ef6-136">*This section applies to Blazor Server.*</span></span>

Blazor<span data-ttu-id="02ef6-137">Sunucu uygulamaları, sunucu bağlantısı kurumadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="02ef6-137"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="02ef6-138">Bu, `_Host.cshtml` Razor sayfada ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="02ef6-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="02ef6-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="02ef6-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="02ef6-140">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="02ef6-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="02ef6-141">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="02ef6-142">Description</span><span class="sxs-lookup"><span data-stu-id="02ef6-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="02ef6-143">Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="02ef6-144">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="02ef6-145">Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="02ef6-146">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="02ef6-146">Output from the component isn't included.</span></span> <span data-ttu-id="02ef6-147">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="02ef6-148">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="02ef6-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="02ef6-149">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="02ef6-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="02ef6-150">SignalRSunucu uygulamaları için istemciyi Blazor yapılandırma</span><span class="sxs-lookup"><span data-stu-id="02ef6-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="02ef6-151">*Bu bölüm sunucu için geçerlidir Blazor .*</span><span class="sxs-lookup"><span data-stu-id="02ef6-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="02ef6-152">Bazen, SignalR sunucu uygulamaları tarafından kullanılan istemciyi yapılandırmanız gerekir Blazor .</span><span class="sxs-lookup"><span data-stu-id="02ef6-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="02ef6-153">Örneğin, SignalR bir bağlantı sorununu tanılamak için istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="02ef6-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="02ef6-154">SignalRİstemcisini dosyada yapılandırmak için `Pages/_Host.cshtml` :</span><span class="sxs-lookup"><span data-stu-id="02ef6-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="02ef6-155">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="02ef6-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="02ef6-156">`Blazor.start`Oluşturucuyu belirten bir yapılandırma nesnesi çağırın ve geçirin SignalR .</span><span class="sxs-lookup"><span data-stu-id="02ef6-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="02ef6-157">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="02ef6-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
