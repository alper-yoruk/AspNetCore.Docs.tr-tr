---
title: ASP.NET Core Blazor yapılandırması
author: guardrex
description: BlazorUygulama ayarları, kimlik doğrulaması ve günlüğe kaydetme yapılandırması dahil olmak üzere uygulamaların yapılandırılması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f78803a3954feb98a39f26874b9de0aa08dc6327
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445222"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="76a2f-103">ASP.NET Core Blazor yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="76a2f-104">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="76a2f-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="76a2f-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="76a2f-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor WebAssembly<span data-ttu-id="76a2f-106">yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="76a2f-106"> loads configuration from:</span></span>

* <span data-ttu-id="76a2f-107">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="76a2f-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="76a2f-108">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="76a2f-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="76a2f-109">Tüm sağlayıcılar, uygulamalar için uygun değildir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="76a2f-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="76a2f-110">İçin desteklenen sağlayıcıların açıklanması, ' nin Blazor WebAssembly [ Blazor (DotNet/AspNetCore.Docs #18134) yapılandırma sağlayıcılarını açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="76a2f-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="76a2f-111">Bir uygulamadaki yapılandırma Blazor WebAssembly kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="76a2f-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="76a2f-112">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="76a2f-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="76a2f-113">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="76a2f-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="76a2f-114">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-114">App settings configuration</span></span>

<span data-ttu-id="76a2f-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="76a2f-116"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="76a2f-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="76a2f-117">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-117">Provider configuration</span></span>

<span data-ttu-id="76a2f-118">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="76a2f-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="76a2f-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-119">`Program.Main`:</span></span>

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

<span data-ttu-id="76a2f-120"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="76a2f-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="76a2f-121">Klasörden yapılandırmaya diğer yapılandırma dosyalarını okumak için `wwwroot` , bir, <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="76a2f-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="76a2f-122">Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="76a2f-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="76a2f-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="76a2f-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="76a2f-125">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-125">Authentication configuration</span></span>

<span data-ttu-id="76a2f-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="76a2f-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="76a2f-128">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-128">Logging configuration</span></span>

<span data-ttu-id="76a2f-129">İçin bir paket başvurusu ekleyin [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="76a2f-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="76a2f-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="76a2f-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="76a2f-132">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="76a2f-132">Host builder configuration</span></span>

<span data-ttu-id="76a2f-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="76a2f-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="76a2f-134">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="76a2f-134">Cached configuration</span></span>

<span data-ttu-id="76a2f-135">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="76a2f-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="76a2f-136">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="76a2f-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="76a2f-137">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="76a2f-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="76a2f-138">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="76a2f-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="76a2f-139">PWA 'nın `service-worker.js` ve `service-worker-assets.js` dosyalarının derleme üzerinde yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını ziyaret etmelidir.</span><span class="sxs-lookup"><span data-stu-id="76a2f-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="76a2f-140">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="76a2f-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
