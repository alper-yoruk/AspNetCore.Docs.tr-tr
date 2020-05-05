---
title: ASP.NET Core Blazor barındırma modeli yapılandırması
author: guardrex
description: Bileşenleri Razor sayfalar Blazor ve MVC uygulamalarıyla tümleştirme Razor dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772080"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="60bcf-103">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="60bcf-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="60bcf-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="60bcf-105">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="60bcf-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="60bcf-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="60bcf-107">Ortam</span><span class="sxs-lookup"><span data-stu-id="60bcf-107">Environment</span></span>

<span data-ttu-id="60bcf-108">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="60bcf-109">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="60bcf-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="60bcf-110">Barındırılan bir Blazor WebAssembly uygulaması, `blazor-environment` üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan ortamı seçer.</span><span class="sxs-lookup"><span data-stu-id="60bcf-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="60bcf-111">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-111">The value of the header is the environment.</span></span> <span data-ttu-id="60bcf-112">Barındırılan Blazor uygulaması ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="60bcf-113">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments>..</span><span class="sxs-lookup"><span data-stu-id="60bcf-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="60bcf-114">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, geliştirme `blazor-environment` ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="60bcf-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="60bcf-115">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60bcf-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="60bcf-116">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60bcf-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="60bcf-117">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="60bcf-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="60bcf-118">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> *Web. config* dosyasını kullanmak için, bkz..</span><span class="sxs-lookup"><span data-stu-id="60bcf-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="60bcf-119">Ekleme `IWebAssemblyHostEnvironment` tarafından bir bileşende uygulamanın ortamını edinin ve `Environment` özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="60bcf-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="60bcf-120">Başlangıç sırasında, geliştiricilerin `WebAssemblyHostBuilder` kendi kodlarında `IWebAssemblyHostEnvironment` ortama özgü `HostEnvironment` mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="60bcf-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="60bcf-121">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="60bcf-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="60bcf-122">' Isenvironment ("{ortam adı}")</span><span class="sxs-lookup"><span data-stu-id="60bcf-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="60bcf-123">Özelliği `IWebAssemblyHostEnvironment.BaseAddress` , `NavigationManager` hizmet kullanılamadığında başlatma sırasında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="60bcf-124">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="60bcf-124">Configuration</span></span>

<span data-ttu-id="60bcf-125">Blazor WebAssembly yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="60bcf-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="60bcf-126">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="60bcf-126">App settings files by default:</span></span>
  * <span data-ttu-id="60bcf-127">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="60bcf-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="60bcf-128">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="60bcf-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="60bcf-129">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="60bcf-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="60bcf-130">Tüm sağlayıcılar Blazor WebAssembly uygulamalarına uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="60bcf-131">Blazor WebAssembly için desteklenen sağlayıcıların açıklanması, Blazor pro5 [(DotNet/AspNetCore. Docs #18134) için yapılandırma sağlayıcıları açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="60bcf-132">Blazor WebAssembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="60bcf-133">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="60bcf-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="60bcf-134">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index>..</span><span class="sxs-lookup"><span data-stu-id="60bcf-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="60bcf-135">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-135">App settings configuration</span></span>

<span data-ttu-id="60bcf-136">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60bcf-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="60bcf-137">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="60bcf-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="60bcf-138">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-138">Provider configuration</span></span>

<span data-ttu-id="60bcf-139">Aşağıdaki örnek, ek yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="60bcf-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="60bcf-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60bcf-140">`Program.Main`:</span></span>

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

<span data-ttu-id="60bcf-141">Yapılandırma verilerine <xref:Microsoft.Extensions.Configuration.IConfiguration> erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="60bcf-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="60bcf-142">*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için dosyanın içeriğini almak üzere bir `HttpClient` kullanın.</span><span class="sxs-lookup"><span data-stu-id="60bcf-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="60bcf-143">Bu yaklaşım kullanıldığında, mevcut `HttpClient` hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="60bcf-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="60bcf-144">*Wwwroot/otomobil. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60bcf-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="60bcf-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60bcf-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="60bcf-146">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-146">Authentication configuration</span></span>

<span data-ttu-id="60bcf-147">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60bcf-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="60bcf-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60bcf-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="60bcf-149">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-149">Logging configuration</span></span>

<span data-ttu-id="60bcf-150">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60bcf-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="60bcf-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60bcf-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="60bcf-152">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="60bcf-152">Host builder configuration</span></span>

<span data-ttu-id="60bcf-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60bcf-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="60bcf-154">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="60bcf-154">Cached configuration</span></span>

<span data-ttu-id="60bcf-155">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="60bcf-156">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60bcf-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="60bcf-157">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="60bcf-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="60bcf-158">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="60bcf-159">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="60bcf-160">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates>..</span><span class="sxs-lookup"><span data-stu-id="60bcf-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="60bcf-161">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="60bcf-161">Logging</span></span>

<span data-ttu-id="60bcf-162">Blazor WebAssembly günlük desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="60bcf-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="60bcf-163">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="60bcf-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="60bcf-164">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="60bcf-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="60bcf-165">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="60bcf-166">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="60bcf-167">Kullanıcı arabirimini özelleştirmek için, *_Host. cshtml* Razor sayfasında `id` öğesinin `components-reconnect-modal` içeren `<body>` bir öğesi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="60bcf-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="60bcf-168">Aşağıdaki tabloda `components-reconnect-modal` ÖĞESINE uygulanan CSS sınıfları açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="60bcf-169">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="60bcf-169">CSS class</span></span>                       | <span data-ttu-id="60bcf-170">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="60bcf-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="60bcf-171">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="60bcf-171">A lost connection.</span></span> <span data-ttu-id="60bcf-172">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="60bcf-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="60bcf-173">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="60bcf-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="60bcf-174">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="60bcf-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="60bcf-175">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="60bcf-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="60bcf-176">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="60bcf-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="60bcf-177">Yeniden bağlanmayı denemek için çağrısı `window.Blazor.reconnect()`yapın.</span><span class="sxs-lookup"><span data-stu-id="60bcf-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="60bcf-178">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="60bcf-178">Reconnection rejected.</span></span> <span data-ttu-id="60bcf-179">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="60bcf-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="60bcf-180">Uygulamayı yeniden yüklemek için çağrısı `location.reload()`yapın.</span><span class="sxs-lookup"><span data-stu-id="60bcf-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="60bcf-181">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="60bcf-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="60bcf-182">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="60bcf-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="60bcf-183">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="60bcf-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="60bcf-184">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="60bcf-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="60bcf-185">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="60bcf-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="60bcf-186">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="60bcf-186">Render mode</span></span>

<span data-ttu-id="60bcf-187">Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="60bcf-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="60bcf-188">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="60bcf-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="60bcf-189">`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="60bcf-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="60bcf-190">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="60bcf-191">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="60bcf-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="60bcf-192">Açıklama</span><span class="sxs-lookup"><span data-stu-id="60bcf-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="60bcf-193">Bileşeni statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="60bcf-194">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="60bcf-195">Blazor Sunucu uygulaması için bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="60bcf-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="60bcf-196">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-196">Output from the component isn't included.</span></span> <span data-ttu-id="60bcf-197">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60bcf-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="60bcf-198">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="60bcf-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="60bcf-199">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="60bcf-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="60bcf-200">Sunucu uygulamaları SignalR için Blazor istemciyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="60bcf-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="60bcf-201">Bazen, sunucu uygulamaları tarafından SignalR Blazor kullanılan istemciyi yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="60bcf-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="60bcf-202">Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60bcf-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="60bcf-203">İstemciyi, SignalR *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="60bcf-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="60bcf-204">Betiğin etiketine bir `autostart="false"` `<script>` `blazor.server.js` ekleyin.</span><span class="sxs-lookup"><span data-stu-id="60bcf-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="60bcf-205">Oluşturucuyu belirten bir yapılandırma nesnesi çağırın `Blazor.start` ve geçirin. SignalR</span><span class="sxs-lookup"><span data-stu-id="60bcf-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="60bcf-206">Günlüğe Kaydetme</span><span class="sxs-lookup"><span data-stu-id="60bcf-206">Logging</span></span>

<span data-ttu-id="60bcf-207">Sunucu günlüğü desteği Blazor hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor>..</span><span class="sxs-lookup"><span data-stu-id="60bcf-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
