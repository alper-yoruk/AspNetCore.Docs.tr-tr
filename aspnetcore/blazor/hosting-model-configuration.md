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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="3b2a9-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3b2a9-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="3b2a9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3b2a9-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="3b2a9-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3b2a9-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="3b2a9-107">Ortam</span><span class="sxs-lookup"><span data-stu-id="3b2a9-107">Environment</span></span>

<span data-ttu-id="3b2a9-108">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3b2a9-109">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="3b2a9-110">Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan ortamı seçer.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="3b2a9-111">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-111">The value of the header is the environment.</span></span> <span data-ttu-id="3b2a9-112">Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="3b2a9-113">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments>..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3b2a9-114">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, geliştirme `blazor-environment` ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="3b2a9-115">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="3b2a9-116">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="3b2a9-117">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="3b2a9-118">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *Web. config* dosyasını kullanmak için, bkz..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="3b2a9-119">Ekleme `IWebAssemblyHostEnvironment` tarafından bir bileşende uygulamanın ortamını edinin ve `Environment` özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="3b2a9-120">Başlangıç sırasında, geliştiricilerin `WebAssemblyHostBuilder` kendi kodlarında `IWebAssemblyHostEnvironment` ortama özgü `HostEnvironment` mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="3b2a9-121">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="3b2a9-122">' Isenvironment ("{ortam adı}")</span><span class="sxs-lookup"><span data-stu-id="3b2a9-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="3b2a9-123">Özelliği `IWebAssemblyHostEnvironment.BaseAddress` , `NavigationManager` hizmet kullanılamadığında başlatma sırasında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="3b2a9-124">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3b2a9-124">Configuration</span></span>

<span data-ttu-id="3b2a9-125">Blazor WebAssembly yapılandırmayı şunları destekler:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="3b2a9-126">Uygulama ayarları dosyaları için varsayılan olarak [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) :</span><span class="sxs-lookup"><span data-stu-id="3b2a9-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="3b2a9-127">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3b2a9-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="3b2a9-128">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="3b2a9-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="3b2a9-129">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="3b2a9-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="3b2a9-130">Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3b2a9-131">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="3b2a9-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="3b2a9-132">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index>..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="3b2a9-133">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-133">App settings configuration</span></span>

<span data-ttu-id="3b2a9-134">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="3b2a9-135">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="3b2a9-136">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-136">Provider configuration</span></span>

<span data-ttu-id="3b2a9-137">Aşağıdaki örnek, ek yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> sağlamak için bir ve [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) kullanır:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="3b2a9-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-138">`Program.Main`:</span></span>

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

<span data-ttu-id="3b2a9-139">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="3b2a9-140">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-140">Authentication configuration</span></span>

<span data-ttu-id="3b2a9-141">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="3b2a9-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="3b2a9-143">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-143">Logging configuration</span></span>

<span data-ttu-id="3b2a9-144">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="3b2a9-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="3b2a9-146">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3b2a9-146">Host builder configuration</span></span>

<span data-ttu-id="3b2a9-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="3b2a9-148">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3b2a9-148">Cached configuration</span></span>

<span data-ttu-id="3b2a9-149">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3b2a9-150">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3b2a9-151">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3b2a9-152">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3b2a9-153">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3b2a9-154">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates>..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="3b2a9-155">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="3b2a9-155">Logging</span></span>

<span data-ttu-id="3b2a9-156">Blazor WebAssembly günlük desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="3b2a9-157">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3b2a9-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3b2a9-158">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="3b2a9-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3b2a9-159">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3b2a9-160">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3b2a9-161">Kullanıcı arabirimini özelleştirmek için, *_Host. cshtml* Razor sayfasında `id` öğesinin `components-reconnect-modal` içeren `<body>` bir öğesi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3b2a9-162">Aşağıdaki tabloda `components-reconnect-modal` ÖĞESINE uygulanan CSS sınıfları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3b2a9-163">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="3b2a9-163">CSS class</span></span>                       | <span data-ttu-id="3b2a9-164">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="3b2a9-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="3b2a9-165">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-165">A lost connection.</span></span> <span data-ttu-id="3b2a9-166">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="3b2a9-167">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="3b2a9-168">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="3b2a9-169">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="3b2a9-170">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3b2a9-171">Yeniden bağlanmayı denemek için çağrısı `window.Blazor.reconnect()`yapın.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="3b2a9-172">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-172">Reconnection rejected.</span></span> <span data-ttu-id="3b2a9-173">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3b2a9-174">Uygulamayı yeniden yüklemek için çağrısı `location.reload()`yapın.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3b2a9-175">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="3b2a9-176">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3b2a9-177">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3b2a9-178">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3b2a9-179">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="3b2a9-180">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="3b2a9-180">Render mode</span></span>

<span data-ttu-id="3b2a9-181">Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3b2a9-182">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="3b2a9-183">`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="3b2a9-184">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="3b2a9-185">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="3b2a9-186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="3b2a9-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="3b2a9-187">Bileşeni statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3b2a9-188">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="3b2a9-189">Blazor Sunucu uygulaması için bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3b2a9-190">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-190">Output from the component isn't included.</span></span> <span data-ttu-id="3b2a9-191">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="3b2a9-192">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3b2a9-193">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="3b2a9-194">Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3b2a9-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="3b2a9-195">Bazen, sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="3b2a9-196">Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="3b2a9-197">İstemciyi, SignalR *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="3b2a9-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="3b2a9-198">Betiğin etiketine bir `autostart="false"` `<script>` `blazor.server.js` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3b2a9-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3b2a9-199">Oluşturucuyu belirten bir yapılandırma nesnesi çağırın `Blazor.start` ve geçirin. SignalR</span><span class="sxs-lookup"><span data-stu-id="3b2a9-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="3b2a9-200">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="3b2a9-200">Logging</span></span>

<span data-ttu-id="3b2a9-201">Sunucu günlüğü desteği Blazor hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="3b2a9-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
