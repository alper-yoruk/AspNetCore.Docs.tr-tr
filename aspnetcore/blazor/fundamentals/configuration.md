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
# <a name="aspnet-core-blazor-configuration"></a>ASP.NET Core Blazor yapılandırması

> [!NOTE]
> Bu konu için geçerlidir Blazor WebAssembly . ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..

Blazor WebAssemblyyapılandırmayı şuradan yükler:

* Uygulama ayarları dosyaları varsayılan olarak:
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) . Tüm sağlayıcılar, uygulamalar için uygun değildir Blazor WebAssembly . İçin desteklenen sağlayıcıların açıklanması, ' nin Blazor WebAssembly [ Blazor (DotNet/AspNetCore.Docs #18134) yapılandırma sağlayıcılarını açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.

> [!WARNING]
> Bir uygulamadaki yapılandırma Blazor WebAssembly kullanıcılar tarafından görülebilir. **Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**

Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..

## <a name="app-settings-configuration"></a>Uygulama ayarları yapılandırması

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a>Sağlayıcı yapılandırması

Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:

`Program.Main`:

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

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:

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

Klasörden yapılandırmaya diğer yapılandırma dosyalarını okumak için `wwwroot` , bir, <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak için kullanın. Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

## <a name="authentication-configuration"></a>Kimlik doğrulama yapılandırması

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Günlüğe kaydetme yapılandırması

İçin bir paket başvurusu ekleyin [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konak Oluşturucu yapılandırması

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Önbelleğe alınmış yapılandırma

Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır. [Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz. Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:

* Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.
* PWA 'nın `service-worker.js` ve `service-worker-assets.js` dosyalarının derleme üzerinde yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını ziyaret etmelidir.

Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..
