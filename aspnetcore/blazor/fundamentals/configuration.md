---
title: ASP.NET Core Blazor yapılandırması
author: guardrex
description: BlazorUygulama ayarları, kimlik doğrulaması ve günlüğe kaydetme yapılandırması dahil olmak üzere uygulamaların yapılandırılması hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485973"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="56485-103">ASP.NET Core Blazor yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56485-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="56485-104">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="56485-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="56485-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="56485-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="56485-106">Blazor WebAssembly Aşağıdaki uygulama ayarları dosyalarından yapılandırmayı varsayılan olarak yükler:</span><span class="sxs-lookup"><span data-stu-id="56485-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="56485-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="56485-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="56485-108">`wwwroot/appsettings.{ENVIRONMENT}.json`Burada `{ENVIRONMENT}` yer tutucu, uygulamanın [çalışma zamanı ortamıdır](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="56485-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="56485-109">Uygulama tarafından kaydedilen diğer yapılandırma sağlayıcıları da yapılandırma sağlayabilir, ancak tüm sağlayıcılar veya sağlayıcı özellikleri uygulamalar için uygun değildir Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="56485-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="56485-110">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration): sağlayıcı, istemci gizli senaryolarıyla yönetilen kimlik ve uygulama kimliği (istemci kimliği) için desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="56485-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="56485-111">İstemci Blazor WebAssembly sırrı, Azure Key Vault hizmetine erişmek için istemci tarafında güvenli hale getirilmediği için, istemci gizli anahtarı ile uygulama kimliği hiçbir ASP.NET Core uygulama için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="56485-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="56485-112">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app): Blazor WebAssembly Blazor WebAssembly uygulamalar Azure 'da bir sunucuda çalıştırılmadığından, sağlayıcı uygulamalar için uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="56485-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="56485-113">Bir uygulamadaki yapılandırma Blazor WebAssembly kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="56485-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="56485-114">**Uygulama gizli dizilerini, kimlik bilgilerini veya diğer gizli verileri bir uygulamanın yapılandırmasında depolamayin Blazor WebAssembly .**</span><span class="sxs-lookup"><span data-stu-id="56485-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="56485-115">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="56485-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="56485-116">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56485-116">App settings configuration</span></span>

<span data-ttu-id="56485-117">Uygulama ayarları dosyalarındaki yapılandırma varsayılan olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="56485-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="56485-118">Aşağıdaki örnekte, bir kullanıcı arabirimi yapılandırma değeri bir uygulama ayarları dosyasında depolanır ve Blazor Framework tarafından otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="56485-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="56485-119">Değer bir bileşen tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="56485-119">The value is read by a component.</span></span>

<span data-ttu-id="56485-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="56485-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="56485-121"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.</span><span class="sxs-lookup"><span data-stu-id="56485-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="56485-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="56485-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="56485-123">Klasörden yapılandırmaya diğer yapılandırma dosyalarını okumak için `wwwroot` , bir, <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="56485-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="56485-124">Aşağıdaki örnek, bir yapılandırma dosyasını ( `cars.json` ) uygulamanın yapılandırmasına okur.</span><span class="sxs-lookup"><span data-stu-id="56485-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="56485-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="56485-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="56485-126">Ad alanını şu <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> şekilde ekleyin `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="56485-127">' `Program.Main` De `Program.cs` , var olan <xref:System.Net.Http.HttpClient> hizmet kaydını, dosyayı okumak için istemcisini kullanacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="56485-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="56485-128">EF Core ile özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="56485-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="56485-129">' De gösterilen EF Core olan özel yapılandırma sağlayıcısı <xref:fundamentals/configuration/index#custom-configuration-provider> uygulamalarla birlikte kullanılır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="56485-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="56485-130">Uygulamalar ile yüklenen veritabanı bağlantı dizeleri ve veritabanları Blazor WebAssembly güvenli değildir ve hassas verileri depolamak için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="56485-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="56485-131">[`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)Uygulamanın proje dosyasına ve için paket başvurularını ekleyin [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) .</span><span class="sxs-lookup"><span data-stu-id="56485-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="56485-132">İçinde açıklanan EF Core yapılandırma sınıflarını ekleyin <xref:fundamentals/configuration/index#custom-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="56485-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="56485-133">Ve için ad alanları ekleyin <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="56485-134">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="56485-135"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.</span><span class="sxs-lookup"><span data-stu-id="56485-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="56485-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="56485-136">`Pages/EFCoreConfig.razor`:</span></span>

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="56485-137">Bellek yapılandırma kaynağı</span><span class="sxs-lookup"><span data-stu-id="56485-137">Memory Configuration Source</span></span>

<span data-ttu-id="56485-138">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> `Program.Main` ek yapılandırma sağlamak için içinde bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="56485-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="56485-139">Ad alanını şu <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> şekilde ekleyin `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="56485-140">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-140">In `Program.Main` of `Program.cs`:</span></span>

```csharp
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

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="56485-141"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.</span><span class="sxs-lookup"><span data-stu-id="56485-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="56485-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="56485-142">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="56485-143">C# kodundaki yapılandırmanın bir bölümünü ile elde edin <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="56485-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="56485-144">Aşağıdaki örnek, `wheels` önceki örnekteki yapılandırma için bölümünü edinir:</span><span class="sxs-lookup"><span data-stu-id="56485-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="56485-145">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56485-145">Authentication configuration</span></span>

<span data-ttu-id="56485-146">Uygulama ayarları dosyasında kimlik doğrulama yapılandırması sağlayın.</span><span class="sxs-lookup"><span data-stu-id="56485-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="56485-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="56485-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="56485-148">İçindeki bir sağlayıcının yapılandırmasını yükleyin Identity <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="56485-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="56485-149">Aşağıdaki örnek, bir [OIDC sağlayıcısı](xref:blazor/security/webassembly/standalone-with-authentication-library)için yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="56485-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="56485-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="56485-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="56485-151">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56485-151">Logging configuration</span></span>

<span data-ttu-id="56485-152">Uygulamanın proje dosyasına bir paket başvurusu ekleyin [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) .</span><span class="sxs-lookup"><span data-stu-id="56485-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="56485-153">Uygulama ayarları dosyasında günlük yapılandırması ' nı belirtin.</span><span class="sxs-lookup"><span data-stu-id="56485-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="56485-154">Günlüğe kaydetme yapılandırması ' de yüklenir `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="56485-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="56485-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="56485-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="56485-156">Ad alanını şu <xref:Microsoft.Extensions.Logging?displayProperty=fullName> şekilde ekleyin `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="56485-157">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="56485-158">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56485-158">Host builder configuration</span></span>

<span data-ttu-id="56485-159">İçinden konak Oluşturucu yapılandırmasını okuyun <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="56485-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="56485-160">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="56485-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="56485-161">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56485-161">Cached configuration</span></span>

<span data-ttu-id="56485-162">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="56485-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="56485-163">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56485-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="56485-164">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="56485-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="56485-165">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="56485-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="56485-166">PWA 'nın `service-worker.js` ve `service-worker-assets.js` dosyalarının derleme üzerinde yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını ziyaret etmelidir.</span><span class="sxs-lookup"><span data-stu-id="56485-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="56485-167">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="56485-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
