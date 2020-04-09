---
title: ASP.NET Blazor Core hosting modeli yapılandırma
author: guardrex
description: Razor Blazor bileşenlerinin Razor Pages ve MVC uygulamalarına nasıl entegre edilebildiğini de içeren model yapılandırmasını barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306427"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="3ba4d-103">ASP.NET Core Blazor hosting modeli yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3ba4d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3ba4d-104">Yazar: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3ba4d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3ba4d-105">Bu makalede, barındırma modeli yapılandırmakapsar.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="3ba4d-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3ba4d-106">Blazor WebAssembly</span></span>

<span data-ttu-id="3ba4d-107">Core 3.2 Preview 3 sürümüASP.NET itibariyle Blazor WebAssembly aşağıdakilerden yapılandırmayı destekler:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="3ba4d-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="3ba4d-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="3ba4d-109">*wwwroot/appsettings adresinde n için. {ÇEVRE}.json*</span><span class="sxs-lookup"><span data-stu-id="3ba4d-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="3ba4d-110">Blazor Barındırılan bir uygulamada, [çalışma zamanı ortamı](xref:fundamentals/environments) sunucu uygulamasının değeriyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="3ba4d-111">Uygulamayı yerel olarak çalıştırırken, ortam Geliştirme varsayılan olarak karşılığını alır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3ba4d-112">Uygulama yayımlandığında, ortam Üretim varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="3ba4d-113">Ortamın nasıl yapılandırılabildiğini de içeren <xref:fundamentals/environments>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="3ba4d-114">Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3ba4d-115">**Uygulama sırlarını veya kimlik bilgilerini yapılandırmada saklamayın.**</span><span class="sxs-lookup"><span data-stu-id="3ba4d-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="3ba4d-116">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3ba4d-117">[Aşamalı Web Uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile yapılandırma dosyalarını yalnızca yeni bir dağıtım oluştururken güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3ba4d-118">Dağıtımlar arasında yapılandırma dosyalarını düzenlemenin hiçbir etkisi yoktur, çünkü:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3ba4d-119">Kullanıcılar, kullanmaya devam ettikleri dosyaların sürümlerini önbelleğe aldılar.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3ba4d-120">PWA'nın *hizmet-worker.js* ve *hizmet-işçi-varlıklar.js* dosyaları derleme üzerine yeniden oluşturulmalıdır, bu da kullanıcının bir sonraki çevrimiçi ziyaretinde uygulamaya uygulamanın yeniden dağıtıldığını bildiren bir sinyal verir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3ba4d-121">Arka plan güncelleştirmelerinin PWA'lar tarafından <xref:blazor/progressive-web-app#background-updates>nasıl işlendiği hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="3ba4d-122">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3ba4d-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3ba4d-123">Bağlantı durumunu UI'de yansıtma</span><span class="sxs-lookup"><span data-stu-id="3ba4d-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3ba4d-124">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmaya çalışırken varsayılan kullanıcı arabirimi kullanıcıya görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3ba4d-125">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3ba4d-126">UI'yi özelleştirmek `id` için, *_Host.cshtml* Razor sayfasındaki öğeyi içeren bir `components-reconnect-modal` `<body>` öğe tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3ba4d-127">Aşağıdaki tabloda `components-reconnect-modal` öğeye uygulanan CSS sınıfları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3ba4d-128">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="3ba4d-128">CSS class</span></span>                       | <span data-ttu-id="3ba4d-129">Gösterir&hellip;</span><span class="sxs-lookup"><span data-stu-id="3ba4d-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="3ba4d-130">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-130">A lost connection.</span></span> <span data-ttu-id="3ba4d-131">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="3ba4d-132">Modal'ı göster.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="3ba4d-133">Etkin bir bağlantı sunucuya yeniden kurulur.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="3ba4d-134">Modal'ı sakla.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="3ba4d-135">Yeniden bağlantı, büyük olasılıkla bir ağ hatası nedeniyle başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3ba4d-136">Yeniden bağlanmayı denemek `window.Blazor.reconnect()`için.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="3ba4d-137">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-137">Reconnection rejected.</span></span> <span data-ttu-id="3ba4d-138">Sunucuya ulaşıldı ancak bağlantıyı reddetti ve kullanıcının sunucudaki durumu kaybolur.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3ba4d-139">Uygulamayı yeniden yüklemek için `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3ba4d-140">Bu bağlantı durumu şu anda neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="3ba4d-141">Sunucu tarafındaki devrede bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3ba4d-142">İstemci, sunucunun kullanıcının durumunu bırakabilecek kadar uzun süre bağlantısı kesildi.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3ba4d-143">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3ba4d-144">Sunucu yeniden başlatılır veya uygulamanın alt işlemi geri dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="3ba4d-145">Render modu</span><span class="sxs-lookup"><span data-stu-id="3ba4d-145">Render mode</span></span>

<span data-ttu-id="3ba4d-146">Blazor Server uygulamaları, sunucuya istemci bağlantısı kurulmadan önce ui'yi sunucuda önceden işlemek için varsayılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3ba4d-147">Bu *_Host.cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="3ba4d-148">`RenderMode`bileşenin:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="3ba4d-149">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="3ba4d-150">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="3ba4d-151">Açıklama</span><span class="sxs-lookup"><span data-stu-id="3ba4d-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="3ba4d-152">Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3ba4d-153">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="3ba4d-154">Blazor Sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3ba4d-155">Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-155">Output from the component isn't included.</span></span> <span data-ttu-id="3ba4d-156">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="3ba4d-157">Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3ba4d-158">Sunucu bileşenlerinin statik bir HTML sayfasından görüntülenmeleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="3ba4d-159">Razor sayfalarından ve görünümlerinden durumlu etkileşimli bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="3ba4d-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="3ba4d-160">Bir Razor sayfasına veya görünümüne devletli etkileşimli bileşenler eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="3ba4d-161">Sayfa veya görünüm işlendiğinde:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-161">When the page or view renders:</span></span>

* <span data-ttu-id="3ba4d-162">Bileşen sayfa veya görünüm le önceden işlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="3ba4d-163">Ön işleme için kullanılan ilk bileşen durumu kaybolur.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="3ba4d-164">SignalR Bağlantı kurulduğunda yeni bileşen durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="3ba4d-165">Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="3ba4d-166">Razor sayfalarından ve görünümlerinden etkileşimli olmayan bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="3ba4d-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="3ba4d-167">Aşağıdaki Razor sayfasında bileşen, `Counter` bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="3ba4d-168">`MyComponent` Statik olarak işlendiğiiçin bileşen etkileşimli olamaz.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="3ba4d-169">Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3ba4d-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="3ba4d-170">Bazen, Sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="3ba4d-171">Örneğin, bağlantı sorunu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="3ba4d-172">SignalR *İstemciyi Pages/_Host.cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="3ba4d-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="3ba4d-173">Komut `autostart="false"` dosyası için `<script>` etikete bir öznitelik ekleyin. `blazor.server.js`</span><span class="sxs-lookup"><span data-stu-id="3ba4d-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3ba4d-174">Oluşturucuyu belirten bir yapılandırma `Blazor.start` nesnesinde SignalR arama ve geçiş.</span><span class="sxs-lookup"><span data-stu-id="3ba4d-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
