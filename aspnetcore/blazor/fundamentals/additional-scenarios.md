---
title: 'ASP.NET Core Blazor barındırma modeli yapılandırması'
author: guardrex
description: 'ASP.NET Core barındırma modeli yapılandırmasına yönelik ek senaryolar hakkında bilgi edinin Blazor .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: f8b6e65424948aaa7b28023497bbbf2a1ceb47dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056055"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="69fad-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="69fad-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="69fad-104">[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="69fad-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="69fad-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="69fad-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="69fad-106">SignalR kimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="69fad-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="69fad-107">*Bu bölüm için geçerlidir Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="69fad-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="69fad-108">SignalRTemel alınan istemciyi, cookie s veya http kimlik doğrulama üstbilgileri gibi kimlik bilgilerini gönderecek şekilde yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="69fad-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="69fad-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> Kaynak noktaları arası isteklere ayarlamak için kullanın [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="69fad-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="69fad-110"><xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Seçeneğini seçeneğe atayın:</span><span class="sxs-lookup"><span data-stu-id="69fad-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="69fad-111">Daha fazla bilgi için bkz. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="69fad-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="69fad-112">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="69fad-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="69fad-113">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="69fad-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="69fad-114">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="69fad-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="69fad-115">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="69fad-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="69fad-116">Kullanıcı arabirimini özelleştirmek için, sayfanın içindeki öğesiyle bir öğe `id` tanımlayın `components-reconnect-modal` `<body>` `_Host.cshtml` Razor :</span><span class="sxs-lookup"><span data-stu-id="69fad-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="69fad-117">Uygulamanın stil sayfasına aşağıdakini ekleyin ( `wwwroot/css/app.css` veya `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="69fad-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="69fad-118">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="69fad-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="69fad-119">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="69fad-119">CSS class</span></span>                       | <span data-ttu-id="69fad-120">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="69fad-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="69fad-121">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="69fad-121">A lost connection.</span></span> <span data-ttu-id="69fad-122">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="69fad-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="69fad-123">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="69fad-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="69fad-124">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="69fad-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="69fad-125">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="69fad-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="69fad-126">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="69fad-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="69fad-127">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="69fad-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="69fad-128">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="69fad-128">Reconnection rejected.</span></span> <span data-ttu-id="69fad-129">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="69fad-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="69fad-130">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="69fad-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="69fad-131">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="69fad-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="69fad-132">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="69fad-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="69fad-133">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="69fad-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="69fad-134">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="69fad-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="69fad-135">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="69fad-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="69fad-136">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="69fad-136">Render mode</span></span>

<span data-ttu-id="69fad-137">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="69fad-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="69fad-138">Blazor Server uygulamalar, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="69fad-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="69fad-139">Bu, `_Host.cshtml` Razor sayfada ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="69fad-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="69fad-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="69fad-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="69fad-141">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="69fad-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="69fad-142">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="69fad-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="69fad-143">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="69fad-143">Render mode</span></span> | <span data-ttu-id="69fad-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="69fad-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="69fad-145">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="69fad-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="69fad-146">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="69fad-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="69fad-147">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="69fad-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="69fad-148">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="69fad-148">Output from the component isn't included.</span></span> <span data-ttu-id="69fad-149">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="69fad-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="69fad-150">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="69fad-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="69fad-151">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="69fad-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="69fad-152">BlazorDevresini başlatma</span><span class="sxs-lookup"><span data-stu-id="69fad-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="69fad-153">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="69fad-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="69fad-154">Blazor ServerDosyada uygulamanın [ SignalR devresine](xref:blazor/hosting-models#circuits) el ile başlangıcını yapılandırın `Pages/_Host.cshtml` :</span><span class="sxs-lookup"><span data-stu-id="69fad-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="69fad-155">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="69fad-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="69fad-156">`Blazor.start` `blazor.server.js` Komut dosyasının etiketinden ve kapanış etiketinin içindeyken çağıran bir betik yerleştirin `</body>` .</span><span class="sxs-lookup"><span data-stu-id="69fad-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="69fad-157">`autostart`Devre dışı bırakıldığında, devresine bağımlı olmayan uygulamanın herhangi bir yönü normal şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="69fad-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="69fad-158">Örneğin, istemci tarafı yönlendirme çalışır.</span><span class="sxs-lookup"><span data-stu-id="69fad-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="69fad-159">Ancak, devresine bağlı olan herhangi bir boyut, `Blazor.start` çağrılana kadar çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="69fad-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="69fad-160">Uygulama davranışı, kurulu bir devre olmadan tahmin edilemez.</span><span class="sxs-lookup"><span data-stu-id="69fad-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="69fad-161">Örneğin, devre kesildiğinde bileşen yöntemleri yürütülemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="69fad-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="69fad-162">BlazorBelge hazır olduğunda Başlat</span><span class="sxs-lookup"><span data-stu-id="69fad-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="69fad-163">BlazorBelge hazır olduğunda uygulamayı başlatmak için:</span><span class="sxs-lookup"><span data-stu-id="69fad-163">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="69fad-164">`Promise`El ile başlatılan sonuçlara zincir</span><span class="sxs-lookup"><span data-stu-id="69fad-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="69fad-165">JS birlikte çalışma başlatma gibi ek görevler gerçekleştirmek için, `then` `Promise` el ile gerçekleştirilen bir uygulama başlangıcını kullanarak bu sonuçlara zincir atamak için kullanın Blazor :</span><span class="sxs-lookup"><span data-stu-id="69fad-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="69fad-166">İstemciyi yapılandırma SignalR</span><span class="sxs-lookup"><span data-stu-id="69fad-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="69fad-167">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="69fad-167">Logging</span></span>

<span data-ttu-id="69fad-168">SignalRİstemci günlüğünü yapılandırmak için, `configureSignalR` `configureLogging` istemci Oluşturucu 'da günlük düzeyiyle çağıran bir yapılandırma nesnesi () geçirin:</span><span class="sxs-lookup"><span data-stu-id="69fad-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

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

<span data-ttu-id="69fad-169">Yukarıdaki örnekte, `information` günlük düzeyine eşdeğerdir <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="69fad-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="69fad-170">Yeniden bağlanma işleyicisini değiştirme</span><span class="sxs-lookup"><span data-stu-id="69fad-170">Modify the reconnection handler</span></span>

<span data-ttu-id="69fad-171">Yeniden bağlantı işleyicisinin devre bağlantı olayları, şu gibi özel davranışlar için değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="69fad-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="69fad-172">Bağlantı kesildiğinde kullanıcıya bildirimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="69fad-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="69fad-173">Bir devre bağlıyken günlüğe kaydetme (istemciden) gerçekleştirmek için.</span><span class="sxs-lookup"><span data-stu-id="69fad-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="69fad-174">Bağlantı olaylarını değiştirmek için, aşağıdaki bağlantı değişiklikleri için geri çağırmaları kaydedin:</span><span class="sxs-lookup"><span data-stu-id="69fad-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="69fad-175">Bırakılan bağlantıların kullanımı `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="69fad-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="69fad-176">Kurulan/yeniden kurulan bağlantılar kullanımı `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="69fad-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="69fad-177">**Her ikisi** `onConnectionDown` ve `onConnectionUp` belirtilmelidir:</span><span class="sxs-lookup"><span data-stu-id="69fad-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="69fad-178">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlama</span><span class="sxs-lookup"><span data-stu-id="69fad-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="69fad-179">Yeniden bağlantı yeniden deneme sayısını ve aralığını ayarlamak için, `maxRetries` her yeniden deneme girişimi için izin verilen yeniden deneme sayısını () ve süreyi milisaniye cinsinden ayarlayın ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="69fad-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="69fad-180">Yeniden bağlantı görüntüsünü gizleme veya değiştirme</span><span class="sxs-lookup"><span data-stu-id="69fad-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="69fad-181">Yeniden bağlantı görüntüsünü gizlemek için, yeniden bağlanma işleyicisini `_reconnectionDisplay` boş bir nesneye ayarlayın ( `{}` veya `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="69fad-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="69fad-182">Yeniden bağlantı görüntüsünü değiştirmek için, `_reconnectionDisplay` Önceki örnekte görüntülenecek öğe olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="69fad-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="69fad-183">Yer tutucu, `{ELEMENT ID}` görüntülenecek HTML ÖĞESININ kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="69fad-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="69fad-184">`transition-delay`Kalıcı öğe için UYGULAMANıN CSS () içindeki özelliğini ayarlayarak yeniden bağlantı görüntülenmeden önce gecikmeyi özelleştirin `wwwroot/css/site.css` .</span><span class="sxs-lookup"><span data-stu-id="69fad-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="69fad-185">Aşağıdaki örnek, 500 MS (varsayılan) olan geçiş gecikmesini 1.000 MS (1 saniye) olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="69fad-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="69fad-186">BlazorDevresini istemciden bağlantısını kesin</span><span class="sxs-lookup"><span data-stu-id="69fad-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="69fad-187">Varsayılan olarak, Blazor [ `unload` sayfa etkinliği](https://developer.mozilla.org/docs/Web/API/Window/unload_event) tetiklendiğinde devre dışı bırakıldığında bağlantı kesilir.</span><span class="sxs-lookup"><span data-stu-id="69fad-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="69fad-188">Bu bağlantı devresinin, istemcideki diğer senaryolara karşı bağlantısını kesmek için `Blazor.disconnect` uygun olay işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="69fad-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="69fad-189">Aşağıdaki örnekte, sayfa gizli olduğunda devre dışı bırakıldığında bağlantı kesilir ([ `pagehide` olay](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="69fad-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="69fad-190">Statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="69fad-190">Static files</span></span>

<span data-ttu-id="69fad-191">*Bu bölüm için geçerlidir Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="69fad-191">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="69fad-192">Bir veya diğer yapılandırma ile ek dosya eşlemeleri oluşturmak için <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> aşağıdaki yaklaşımlardan **birini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="69fad-192">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="69fad-193">Aşağıdaki örneklerde, `{EXTENSION}` yer tutucu dosya uzantısıdır ve `{CONTENT TYPE}` yer tutucu içerik türüdür.</span><span class="sxs-lookup"><span data-stu-id="69fad-193">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="69fad-194">() İçindeki () [bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection) aracılığıyla seçenekleri `Startup.ConfigureServices` kullanarak yapılandırın `Startup.cs` <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="69fad-194">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="69fad-195">Bu yaklaşım, hizmet vermek için kullanılan aynı dosya sağlayıcısını yapılandırdığından `blazor.server.js` , özel yapılandırmanızın hizmeti sunmaya engel olmadığından emin olun `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="69fad-195">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="69fad-196">Örneğin, sağlayıcıyı ile yapılandırarak JavaScript dosyaları için eşlemeyi kaldırmayın `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="69fad-196">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="69fad-197"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>() İçinde için iki çağrı kullanın `Startup.Configure` `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="69fad-197">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="69fad-198">İle ilk çağrıda özel dosya sağlayıcısını yapılandırın <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="69fad-198">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="69fad-199">İkinci ara yazılım, `blazor.server.js` Framework tarafından sunulan varsayılan statik dosya yapılandırmasını kullanan hizmet verir Blazor .</span><span class="sxs-lookup"><span data-stu-id="69fad-199">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="69fad-200">`_framework/blazor.server.js` <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> Özel bir statik dosya ara yazılımı yürütmek için kullanarak hizmet vererek kesintiye uğramaktan kaçınabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="69fad-200">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="69fad-201">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="69fad-201">Additional resources</span></span>

* <xref:fundamentals/logging/index>
