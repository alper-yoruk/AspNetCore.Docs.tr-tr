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
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485973"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>ASP.NET Core Blazor yapılandırması

> [!NOTE]
> Bu konu için geçerlidir Blazor WebAssembly . ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/configuration/index> ..

Blazor WebAssembly Aşağıdaki uygulama ayarları dosyalarından yapılandırmayı varsayılan olarak yükler:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`Burada `{ENVIRONMENT}` yer tutucu, uygulamanın [çalışma zamanı ortamıdır](xref:fundamentals/environments).

Uygulama tarafından kaydedilen diğer yapılandırma sağlayıcıları da yapılandırma sağlayabilir, ancak tüm sağlayıcılar veya sağlayıcı özellikleri uygulamalar için uygun değildir Blazor WebAssembly :

* [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration): sağlayıcı, istemci gizli senaryolarıyla yönetilen kimlik ve uygulama kimliği (istemci kimliği) için desteklenmiyor. İstemci Blazor WebAssembly sırrı, Azure Key Vault hizmetine erişmek için istemci tarafında güvenli hale getirilmediği için, istemci gizli anahtarı ile uygulama kimliği hiçbir ASP.NET Core uygulama için önerilmez.
* [Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app): Blazor WebAssembly Blazor WebAssembly uygulamalar Azure 'da bir sunucuda çalıştırılmadığından, sağlayıcı uygulamalar için uygun değildir.

> [!WARNING]
> Bir uygulamadaki yapılandırma Blazor WebAssembly kullanıcılar tarafından görülebilir. **Uygulama gizli dizilerini, kimlik bilgilerini veya diğer gizli verileri bir uygulamanın yapılandırmasında depolamayin Blazor WebAssembly .**

Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..

## <a name="app-settings-configuration"></a>Uygulama ayarları yapılandırması

Uygulama ayarları dosyalarındaki yapılandırma varsayılan olarak yüklenir. Aşağıdaki örnekte, bir kullanıcı arabirimi yapılandırma değeri bir uygulama ayarları dosyasında depolanır ve Blazor Framework tarafından otomatik olarak yüklenir. Değer bir bileşen tarafından okundu.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Klasörden yapılandırmaya diğer yapılandırma dosyalarını okumak için `wwwroot` , bir, <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak için kullanın. Aşağıdaki örnek, bir yapılandırma dosyasını ( `cars.json` ) uygulamanın yapılandırmasına okur.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Ad alanını şu <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> şekilde ekleyin `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration;
```

' `Program.Main` De `Program.cs` , var olan <xref:System.Net.Http.HttpClient> hizmet kaydını, dosyayı okumak için istemcisini kullanacak şekilde değiştirin:

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

## <a name="custom-configuration-provider-with-ef-core"></a>EF Core ile özel yapılandırma sağlayıcısı

' De gösterilen EF Core olan özel yapılandırma sağlayıcısı <xref:fundamentals/configuration/index#custom-configuration-provider> uygulamalarla birlikte kullanılır Blazor WebAssembly .

> [!WARNING]
> Uygulamalar ile yüklenen veritabanı bağlantı dizeleri ve veritabanları Blazor WebAssembly güvenli değildir ve hassas verileri depolamak için kullanılmamalıdır.

[`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)Uygulamanın proje dosyasına ve için paket başvurularını ekleyin [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) .

İçinde açıklanan EF Core yapılandırma sınıflarını ekleyin <xref:fundamentals/configuration/index#custom-configuration-provider> .

Ve için ad alanları ekleyin <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> `Program.cs` :

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

İçinde `Program.Main` `Program.cs` :

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.

`Pages/EFCoreConfig.razor`:

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

## <a name="memory-configuration-source"></a>Bellek yapılandırma kaynağı

Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> `Program.Main` ek yapılandırma sağlamak için içinde bir kullanır.

Ad alanını şu <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> şekilde ekleyin `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

İçinde `Program.Main` `Program.cs` :

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

<xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bir bileşene örnek ekleme.

`Pages/MemoryConfig.razor`:

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

C# kodundaki yapılandırmanın bir bölümünü ile elde edin <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> . Aşağıdaki örnek, `wheels` önceki örnekteki yapılandırma için bölümünü edinir:

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Kimlik doğrulama yapılandırması

Uygulama ayarları dosyasında kimlik doğrulama yapılandırması sağlayın.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

İçindeki bir sağlayıcının yapılandırmasını yükleyin Identity <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> `Program.Main` . Aşağıdaki örnek, bir [OIDC sağlayıcısı](xref:blazor/security/webassembly/standalone-with-authentication-library)için yapılandırmayı yükler.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Günlüğe kaydetme yapılandırması

Uygulamanın proje dosyasına bir paket başvurusu ekleyin [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) . Uygulama ayarları dosyasında günlük yapılandırması ' nı belirtin. Günlüğe kaydetme yapılandırması ' de yüklenir `Program.Main` .

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

Ad alanını şu <xref:Microsoft.Extensions.Logging?displayProperty=fullName> şekilde ekleyin `Program.cs` :

```csharp
using Microsoft.Extensions.Logging;
```

İçinde `Program.Main` `Program.cs` :

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konak Oluşturucu yapılandırması

İçinden konak Oluşturucu yapılandırmasını okuyun <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> `Program.Main` .

İçinde `Program.Main` `Program.cs` :

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Önbelleğe alınmış yapılandırma

Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır. [Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz. Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:

* Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.
* PWA 'nın `service-worker.js` ve `service-worker-assets.js` dosyalarının derleme üzerinde yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını ziyaret etmelidir.

Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..
