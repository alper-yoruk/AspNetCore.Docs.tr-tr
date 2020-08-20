---
title: ASP.NET Core Blazor yapılandırması
author: guardrex
description: BlazorUygulama ayarları, kimlik doğrulaması ve günlüğe kaydetme yapılandırması dahil olmak üzere uygulamaların yapılandırılması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 1607a80ea12cedd82b5996cf1b09e02e1aada2fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628813"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="b884d-103">ASP.NET Core Blazor yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b884d-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="b884d-104">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b884d-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="b884d-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="b884d-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b884d-106">Blazor WebAssembly yapılandırma ayarlarını varsayılan olarak uygulama ayarları dosyalarından yükler:</span><span class="sxs-lookup"><span data-stu-id="b884d-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="b884d-107">Uygulama tarafından kaydedilen diğer yapılandırma sağlayıcıları da yapılandırma sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b884d-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="b884d-108">Tüm sağlayıcılar veya sağlayıcı özellikleri uygulamalar için uygun değildir Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="b884d-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="b884d-109">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration): sağlayıcı, istemci gizli senaryolarıyla yönetilen kimlik ve uygulama kimliği (istemci kimliği) için desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b884d-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="b884d-110">İstemci Blazor WebAssembly sırrı, hizmete erişmek için istemci tarafında güvenli hale getirilmediği için, istemci gizli anahtarı ile uygulama kimliği hiçbir ASP.NET Core uygulama için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="b884d-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="b884d-111">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app): Blazor WebAssembly Blazor WebAssembly uygulamalar Azure 'da bir sunucuda çalıştırılmadığından, sağlayıcı uygulamalar için uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="b884d-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="b884d-112">Bir uygulamadaki yapılandırma Blazor WebAssembly kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="b884d-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="b884d-113">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="b884d-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b884d-114">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="b884d-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="b884d-115">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b884d-115">App settings configuration</span></span>

<span data-ttu-id="b884d-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b884d-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b884d-117"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="b884d-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="b884d-118">EF Core ile özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="b884d-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="b884d-119">' De gösterilen EF Core olan özel yapılandırma sağlayıcısı <xref:fundamentals/configuration/index#custom-configuration-provider> uygulamalarla birlikte kullanılır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b884d-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="b884d-120">Örneğin yapılandırma sağlayıcısını aşağıdaki kodla `Program.Main` ( `Program.cs` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b884d-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="b884d-121"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="b884d-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="b884d-122">Bellek yapılandırma kaynağı</span><span class="sxs-lookup"><span data-stu-id="b884d-122">Memory Configuration Source</span></span>

<span data-ttu-id="b884d-123">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="b884d-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="b884d-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b884d-124">`Program.Main`:</span></span>

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

<span data-ttu-id="b884d-125"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="b884d-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="b884d-126">Klasörden yapılandırmaya diğer yapılandırma dosyalarını okumak için `wwwroot` , bir, <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b884d-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="b884d-127">Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="b884d-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="b884d-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="b884d-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="b884d-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b884d-129">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="b884d-130">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b884d-130">Authentication configuration</span></span>

<span data-ttu-id="b884d-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b884d-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="b884d-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b884d-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="b884d-133">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b884d-133">Logging configuration</span></span>

<span data-ttu-id="b884d-134">İçin bir paket başvurusu ekleyin [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :</span><span class="sxs-lookup"><span data-stu-id="b884d-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="b884d-135">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="b884d-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="b884d-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b884d-136">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="b884d-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b884d-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="b884d-138">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b884d-138">Host builder configuration</span></span>

<span data-ttu-id="b884d-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b884d-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="b884d-140">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b884d-140">Cached configuration</span></span>

<span data-ttu-id="b884d-141">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="b884d-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b884d-142">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b884d-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b884d-143">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="b884d-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b884d-144">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="b884d-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b884d-145">PWA 'nın `service-worker.js` ve `service-worker-assets.js` dosyalarının derleme üzerinde yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını ziyaret etmelidir.</span><span class="sxs-lookup"><span data-stu-id="b884d-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b884d-146">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="b884d-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
