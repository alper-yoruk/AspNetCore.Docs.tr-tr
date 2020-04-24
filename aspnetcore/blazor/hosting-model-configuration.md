---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: Razor bileşenlerini Blazor Razor Pages ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: cf5776109368dc7353d7e21bcad1e947561e7eb4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111064"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="00904-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="00904-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="00904-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="00904-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="00904-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="00904-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="00904-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="00904-107">Ortam</span><span class="sxs-lookup"><span data-stu-id="00904-107">Environment</span></span>

<span data-ttu-id="00904-108">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="00904-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="00904-109">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="00904-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="00904-110">Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan ortamı seçer.</span><span class="sxs-lookup"><span data-stu-id="00904-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="00904-111">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="00904-111">The value of the header is the environment.</span></span> <span data-ttu-id="00904-112">Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="00904-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="00904-113">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments>..</span><span class="sxs-lookup"><span data-stu-id="00904-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="00904-114">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, geliştirme `blazor-environment` ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="00904-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="00904-115">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00904-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="00904-116">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00904-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="00904-117">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="00904-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="00904-118">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *Web. config* dosyasını kullanmak için, bkz..</span><span class="sxs-lookup"><span data-stu-id="00904-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="00904-119">Ekleme `IWebAssemblyHostEnvironment` tarafından bir bileşende uygulamanın ortamını edinin ve `Environment` özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="00904-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="00904-120">Başlangıç sırasında, geliştiricilerin `WebAssemblyHostBuilder` kendi kodlarında `IWebAssemblyHostEnvironment` ortama özgü `HostEnvironment` mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="00904-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="00904-121">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="00904-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="00904-122">' Isenvironment ("{ortam adı}")</span><span class="sxs-lookup"><span data-stu-id="00904-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="00904-123">Özelliği `IWebAssemblyHostEnvironment.BaseAddress` , `NavigationManager` hizmet kullanılamadığında başlatma sırasında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="00904-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="00904-124">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="00904-124">Configuration</span></span>

<span data-ttu-id="00904-125">Blazor WebAssembly yapılandırmayı şunları destekler:</span><span class="sxs-lookup"><span data-stu-id="00904-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="00904-126">Uygulama ayarları dosyaları için varsayılan olarak [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) :</span><span class="sxs-lookup"><span data-stu-id="00904-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="00904-127">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="00904-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="00904-128">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="00904-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="00904-129">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="00904-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="00904-130">Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="00904-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="00904-131">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="00904-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="00904-132">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index>..</span><span class="sxs-lookup"><span data-stu-id="00904-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="00904-133">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-133">App settings configuration</span></span>

<span data-ttu-id="00904-134">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="00904-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="00904-135">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="00904-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="00904-136">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-136">Provider configuration</span></span>

<span data-ttu-id="00904-137">Aşağıdaki örnek, ek yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> sağlamak için bir ve [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) kullanır:</span><span class="sxs-lookup"><span data-stu-id="00904-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="00904-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="00904-138">`Program.Main`:</span></span>

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

<span data-ttu-id="00904-139">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="00904-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="00904-140">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-140">Authentication configuration</span></span>

<span data-ttu-id="00904-141">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="00904-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="00904-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="00904-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="00904-143">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-143">Logging configuration</span></span>

<span data-ttu-id="00904-144">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="00904-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="00904-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="00904-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="00904-146">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="00904-146">Host builder configuration</span></span>

<span data-ttu-id="00904-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="00904-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="00904-148">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="00904-148">Cached configuration</span></span>

<span data-ttu-id="00904-149">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="00904-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="00904-150">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00904-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="00904-151">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="00904-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="00904-152">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="00904-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="00904-153">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00904-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="00904-154">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates>..</span><span class="sxs-lookup"><span data-stu-id="00904-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="00904-155">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="00904-155">Logging</span></span>

<span data-ttu-id="00904-156">Blazor WebAssembly günlük desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="00904-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="00904-157">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="00904-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="00904-158">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="00904-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="00904-159">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="00904-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="00904-160">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="00904-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="00904-161">Kullanıcı arabirimini özelleştirmek için, *_Host. cshtml* Razor sayfasında `id` öğesinin `components-reconnect-modal` içeren `<body>` bir öğesi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="00904-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="00904-162">Aşağıdaki tabloda `components-reconnect-modal` ÖĞESINE uygulanan CSS sınıfları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="00904-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="00904-163">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="00904-163">CSS class</span></span>                       | <span data-ttu-id="00904-164">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="00904-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="00904-165">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="00904-165">A lost connection.</span></span> <span data-ttu-id="00904-166">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="00904-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="00904-167">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="00904-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="00904-168">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="00904-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="00904-169">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="00904-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="00904-170">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="00904-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="00904-171">Yeniden bağlanmayı denemek için çağrısı `window.Blazor.reconnect()`yapın.</span><span class="sxs-lookup"><span data-stu-id="00904-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="00904-172">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="00904-172">Reconnection rejected.</span></span> <span data-ttu-id="00904-173">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="00904-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="00904-174">Uygulamayı yeniden yüklemek için çağrısı `location.reload()`yapın.</span><span class="sxs-lookup"><span data-stu-id="00904-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="00904-175">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="00904-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="00904-176">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="00904-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="00904-177">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="00904-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="00904-178">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="00904-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="00904-179">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="00904-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="00904-180">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="00904-180">Render mode</span></span>

<span data-ttu-id="00904-181">Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="00904-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="00904-182">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="00904-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="00904-183">`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="00904-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="00904-184">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="00904-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="00904-185">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="00904-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="00904-186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="00904-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="00904-187">Bileşeni statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="00904-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="00904-188">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00904-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="00904-189">Blazor Sunucu uygulaması için bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="00904-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="00904-190">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="00904-190">Output from the component isn't included.</span></span> <span data-ttu-id="00904-191">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00904-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="00904-192">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="00904-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="00904-193">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="00904-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="00904-194">Razor sayfaları ve görünümlerinden durum bilgisi olan etkileşimli bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="00904-194">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="00904-195">Durum bilgisi olan etkileşimli bileşenler Razor sayfasına veya görünümüne eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="00904-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="00904-196">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="00904-196">When the page or view renders:</span></span>

* <span data-ttu-id="00904-197">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00904-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="00904-198">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="00904-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="00904-199">SignalR Bağlantı kurulduunda yeni bileşen durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="00904-199">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="00904-200">Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:</span><span class="sxs-lookup"><span data-stu-id="00904-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="00904-201">Razor sayfaları ve görünümlerinden etkileşimsiz bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="00904-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="00904-202">Aşağıdaki Razor sayfasında, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="00904-202">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="00904-203">Statik `MyComponent` olarak işlendiğinden bileşen etkileşimli olamaz.</span><span class="sxs-lookup"><span data-stu-id="00904-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="00904-204">Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="00904-204">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="00904-205">Bazen, sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="00904-205">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="00904-206">Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00904-206">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="00904-207">İstemciyi, SignalR *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="00904-207">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="00904-208">Betiğin etiketine bir `autostart="false"` `<script>` `blazor.server.js` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00904-208">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="00904-209">Oluşturucuyu belirten bir yapılandırma nesnesi çağırın `Blazor.start` ve geçirin. SignalR</span><span class="sxs-lookup"><span data-stu-id="00904-209">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="00904-210">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="00904-210">Logging</span></span>

<span data-ttu-id="00904-211">Sunucu günlüğü desteği Blazor hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="00904-211">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
