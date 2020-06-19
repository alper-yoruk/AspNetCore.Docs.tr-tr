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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103852"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="71991-103">ASP.NET Core Blazor yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="71991-104">Bu konu, Blazor webassembly için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="71991-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="71991-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="71991-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="71991-106">WebAssembly yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="71991-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="71991-107">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="71991-107">App settings files by default:</span></span>
  * <span data-ttu-id="71991-108">*Wwwroot/appsettings.jsaçık*</span><span class="sxs-lookup"><span data-stu-id="71991-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="71991-109">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="71991-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="71991-110">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="71991-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="71991-111">Tüm sağlayıcılar Blazor webassembly uygulamalarına uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="71991-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="71991-112">Wezsembly için desteklenen sağlayıcıların açıklanması Blazor , [ISG için yapılandırma sağlayıcıları Blazor (DotNet/AspNetCore.Docs #18134) açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="71991-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="71991-113">BlazorWebassembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="71991-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="71991-114">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="71991-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="71991-115">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="71991-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="71991-116">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-116">App settings configuration</span></span>

<span data-ttu-id="71991-117">*Wwwroot/appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="71991-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="71991-118"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="71991-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="71991-119">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-119">Provider configuration</span></span>

<span data-ttu-id="71991-120">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="71991-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="71991-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71991-121">`Program.Main`:</span></span>

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

<span data-ttu-id="71991-122"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="71991-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="71991-123">*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak üzere bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="71991-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="71991-124">Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="71991-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="71991-125">*Wwwroot/cars.js*:</span><span class="sxs-lookup"><span data-stu-id="71991-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="71991-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71991-126">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="71991-127">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-127">Authentication configuration</span></span>

<span data-ttu-id="71991-128">*Wwwroot/appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="71991-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="71991-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71991-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="71991-130">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-130">Logging configuration</span></span>

<span data-ttu-id="71991-131">[Microsoft.Extensions.Logging.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)için bir paket başvurusu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="71991-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="71991-132">*Wwwroot/appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="71991-132">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="71991-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71991-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="71991-134">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71991-134">Host builder configuration</span></span>

<span data-ttu-id="71991-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71991-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="71991-136">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="71991-136">Cached configuration</span></span>

<span data-ttu-id="71991-137">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="71991-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="71991-138">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="71991-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="71991-139">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="71991-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="71991-140">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="71991-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="71991-141">PWA 'nın *service-worker.js* ve *service-worker-assets.js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi üzerinde uygulamaya işaret eden, uygulamanın yeniden dağıtıldığını belirten bir daha ziyaret etmelidir.</span><span class="sxs-lookup"><span data-stu-id="71991-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="71991-142">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="71991-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
