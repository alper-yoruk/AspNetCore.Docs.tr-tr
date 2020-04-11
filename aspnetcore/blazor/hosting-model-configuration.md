---
title: ASP.NET Blazor Core hosting modeli yapılandırma
author: guardrex
description: Razor Blazor bileşenlerinin Razor Pages ve MVC uygulamalarına nasıl entegre edilebildiğini de içeren model yapılandırmasını barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: ca1b3ea9092640ca561b3fbe02ddce6f974c525e
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123382"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="4d455-103">ASP.NET Core Blazor hosting modeli yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4d455-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="4d455-104">Yazar: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4d455-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4d455-105">Bu makalede, barındırma modeli yapılandırmakapsar.</span><span class="sxs-lookup"><span data-stu-id="4d455-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="4d455-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4d455-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="4d455-107">Ortam</span><span class="sxs-lookup"><span data-stu-id="4d455-107">Environment</span></span>

<span data-ttu-id="4d455-108">Bir uygulamayı yerel olarak çalıştırırken, ortam Geliştirme varsayılan olarak</span><span class="sxs-lookup"><span data-stu-id="4d455-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="4d455-109">Uygulama yayımlandığında, ortam Üretim varsayılandır.</span><span class="sxs-lookup"><span data-stu-id="4d455-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="4d455-110">Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üstbilgi ekleyerek ortamı tarayıcıya bildiren bir ara yazılım aracılığıyla ortamı sunucudan alır.</span><span class="sxs-lookup"><span data-stu-id="4d455-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="4d455-111">Üstbilginin değeri ortamdır.</span><span class="sxs-lookup"><span data-stu-id="4d455-111">The value of the header is the environment.</span></span> <span data-ttu-id="4d455-112">Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="4d455-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="4d455-113">Ortamın nasıl yapılandırılabildiğini de içeren <xref:fundamentals/environments>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4d455-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4d455-114">Yerel olarak çalışan bağımsız bir uygulama için `blazor-environment` geliştirme sunucusu Geliştirme ortamını belirtmek için üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="4d455-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="4d455-115">Diğer barındırma ortamları için ortamı `blazor-environment` belirtmek için üstbilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4d455-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="4d455-116">IIS için aşağıdaki örnekte, yayınlanan *web.config* dosyasına özel üstbilgi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4d455-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="4d455-117">*web.config* dosyası *depo/Yayın/{TARGET FRAMEWORK}/publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="4d455-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="4d455-118">Uygulama *yayımlama* klasöründe yayımlandığında üzerine yazılmayan IIS için özel bir <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *web.config* dosyası kullanmak için bkz.</span><span class="sxs-lookup"><span data-stu-id="4d455-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="4d455-119">Özelliği enjekte ederek ve okuyarak uygulamanın ortamını bir bileşende elde edin: `IWebAssemblyHostEnvironment` `Environment`</span><span class="sxs-lookup"><span data-stu-id="4d455-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

### <a name="configuration"></a><span data-ttu-id="4d455-120">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4d455-120">Configuration</span></span>

<span data-ttu-id="4d455-121">Core 3.2 Preview 3 sürümüASP.NET itibariyle Blazor WebAssembly aşağıdakilerden yapılandırmayı destekler:</span><span class="sxs-lookup"><span data-stu-id="4d455-121">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="4d455-122">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4d455-122">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="4d455-123">*wwwroot/appsettings adresinde n için. {ÇEVRE}.json*</span><span class="sxs-lookup"><span data-stu-id="4d455-123">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="4d455-124">*wwwroot* klasörüne bir *appsettings.json* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4d455-124">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="4d455-125">Yapılandırma <xref:Microsoft.Extensions.Configuration.IConfiguration> verilerine erişmek için bir bileşene örnek enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="4d455-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="4d455-126">Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="4d455-126">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4d455-127">**Uygulama sırlarını veya kimlik bilgilerini yapılandırmada saklamayın.**</span><span class="sxs-lookup"><span data-stu-id="4d455-127">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="4d455-128">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="4d455-128">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4d455-129">[Aşamalı Web Uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile yapılandırma dosyalarını yalnızca yeni bir dağıtım oluştururken güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d455-129">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4d455-130">Dağıtımlar arasında yapılandırma dosyalarını düzenlemenin hiçbir etkisi yoktur, çünkü:</span><span class="sxs-lookup"><span data-stu-id="4d455-130">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4d455-131">Kullanıcılar, kullanmaya devam ettikleri dosyaların sürümlerini önbelleğe aldılar.</span><span class="sxs-lookup"><span data-stu-id="4d455-131">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4d455-132">PWA'nın *hizmet-worker.js* ve *hizmet-işçi-varlıklar.js* dosyaları derleme üzerine yeniden oluşturulmalıdır, bu da kullanıcının bir sonraki çevrimiçi ziyaretinde uygulamaya uygulamanın yeniden dağıtıldığını bildiren bir sinyal verir.</span><span class="sxs-lookup"><span data-stu-id="4d455-132">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4d455-133">Arka plan güncelleştirmelerinin PWA'lar tarafından <xref:blazor/progressive-web-app#background-updates>nasıl işlendiği hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="4d455-133">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="4d455-134">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="4d455-134">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="4d455-135">Bağlantı durumunu UI'de yansıtma</span><span class="sxs-lookup"><span data-stu-id="4d455-135">Reflect the connection state in the UI</span></span>

<span data-ttu-id="4d455-136">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmaya çalışırken varsayılan kullanıcı arabirimi kullanıcıya görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="4d455-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4d455-137">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4d455-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="4d455-138">UI'yi özelleştirmek `id` için, *_Host.cshtml* Razor sayfasındaki öğeyi içeren bir `components-reconnect-modal` `<body>` öğe tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="4d455-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="4d455-139">Aşağıdaki tabloda `components-reconnect-modal` öğeye uygulanan CSS sınıfları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4d455-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="4d455-140">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="4d455-140">CSS class</span></span>                       | <span data-ttu-id="4d455-141">Gösterir&hellip;</span><span class="sxs-lookup"><span data-stu-id="4d455-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="4d455-142">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="4d455-142">A lost connection.</span></span> <span data-ttu-id="4d455-143">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="4d455-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="4d455-144">Modal'ı göster.</span><span class="sxs-lookup"><span data-stu-id="4d455-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="4d455-145">Etkin bir bağlantı sunucuya yeniden kurulur.</span><span class="sxs-lookup"><span data-stu-id="4d455-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="4d455-146">Modal'ı sakla.</span><span class="sxs-lookup"><span data-stu-id="4d455-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="4d455-147">Yeniden bağlantı, büyük olasılıkla bir ağ hatası nedeniyle başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="4d455-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="4d455-148">Yeniden bağlanmayı denemek `window.Blazor.reconnect()`için.</span><span class="sxs-lookup"><span data-stu-id="4d455-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="4d455-149">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="4d455-149">Reconnection rejected.</span></span> <span data-ttu-id="4d455-150">Sunucuya ulaşıldı ancak bağlantıyı reddetti ve kullanıcının sunucudaki durumu kaybolur.</span><span class="sxs-lookup"><span data-stu-id="4d455-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="4d455-151">Uygulamayı yeniden yüklemek için `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="4d455-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="4d455-152">Bu bağlantı durumu şu anda neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="4d455-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="4d455-153">Sunucu tarafındaki devrede bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="4d455-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="4d455-154">İstemci, sunucunun kullanıcının durumunu bırakabilecek kadar uzun süre bağlantısı kesildi.</span><span class="sxs-lookup"><span data-stu-id="4d455-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="4d455-155">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri bertaraf edilir.</span><span class="sxs-lookup"><span data-stu-id="4d455-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="4d455-156">Sunucu yeniden başlatılır veya uygulamanın alt işlemi geri dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4d455-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="4d455-157">Render modu</span><span class="sxs-lookup"><span data-stu-id="4d455-157">Render mode</span></span>

<span data-ttu-id="4d455-158">Blazor Server uygulamaları, sunucuya istemci bağlantısı kurulmadan önce ui'yi sunucuda önceden işlemek için varsayılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4d455-158">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4d455-159">Bu *_Host.cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="4d455-159">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4d455-160">`RenderMode`bileşenin:</span><span class="sxs-lookup"><span data-stu-id="4d455-160">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="4d455-161">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="4d455-161">Is prerendered into the page.</span></span>
* <span data-ttu-id="4d455-162">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="4d455-162">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="4d455-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4d455-163">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="4d455-164">Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="4d455-164">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4d455-165">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4d455-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="4d455-166">Blazor Sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="4d455-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4d455-167">Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="4d455-167">Output from the component isn't included.</span></span> <span data-ttu-id="4d455-168">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4d455-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="4d455-169">Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4d455-169">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4d455-170">Sunucu bileşenlerinin statik bir HTML sayfasından görüntülenmeleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4d455-170">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="4d455-171">Razor sayfalarından ve görünümlerinden durumlu etkileşimli bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="4d455-171">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="4d455-172">Bir Razor sayfasına veya görünümüne devletli etkileşimli bileşenler eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4d455-172">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4d455-173">Sayfa veya görünüm işlendiğinde:</span><span class="sxs-lookup"><span data-stu-id="4d455-173">When the page or view renders:</span></span>

* <span data-ttu-id="4d455-174">Bileşen sayfa veya görünüm le önceden işlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="4d455-174">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4d455-175">Ön işleme için kullanılan ilk bileşen durumu kaybolur.</span><span class="sxs-lookup"><span data-stu-id="4d455-175">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4d455-176">SignalR Bağlantı kurulduğunda yeni bileşen durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4d455-176">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="4d455-177">Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:</span><span class="sxs-lookup"><span data-stu-id="4d455-177">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="4d455-178">Razor sayfalarından ve görünümlerinden etkileşimli olmayan bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="4d455-178">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="4d455-179">Aşağıdaki Razor sayfasında bileşen, `Counter` bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="4d455-179">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="4d455-180">`MyComponent` Statik olarak işlendiğiiçin bileşen etkileşimli olamaz.</span><span class="sxs-lookup"><span data-stu-id="4d455-180">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="4d455-181">Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4d455-181">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="4d455-182">Bazen, Sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="4d455-182">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="4d455-183">Örneğin, bağlantı sorunu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d455-183">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="4d455-184">SignalR *İstemciyi Pages/_Host.cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="4d455-184">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="4d455-185">Komut `autostart="false"` dosyası için `<script>` etikete bir öznitelik ekleyin. `blazor.server.js`</span><span class="sxs-lookup"><span data-stu-id="4d455-185">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4d455-186">Oluşturucuyu belirten bir yapılandırma `Blazor.start` nesnesinde SignalR arama ve geçiş.</span><span class="sxs-lookup"><span data-stu-id="4d455-186">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
