---
title: ASP.NET Core Blazor ortamları
author: guardrex
description: "' Deki ortamlar Blazor , bir uygulamanın ortamını ayarlama dahil hakkında bilgi edinin Blazor WebAssembly ."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: 8a672b3d2aff4dd2b80465b0f6dac038d299eaa9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014432"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="4cd59-103">ASP.NET Core Blazor ortamları</span><span class="sxs-lookup"><span data-stu-id="4cd59-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="4cd59-104">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4cd59-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4cd59-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="4cd59-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4cd59-106">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="4cd59-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="4cd59-107">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="4cd59-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="4cd59-108">Barındırılan bir Blazor WebAssembly uygulama, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan ortamı seçer `blazor-environment` .</span><span class="sxs-lookup"><span data-stu-id="4cd59-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="4cd59-109">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="4cd59-109">The value of the header is the environment.</span></span> <span data-ttu-id="4cd59-110">Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="4cd59-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="4cd59-111">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="4cd59-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4cd59-112">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="4cd59-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="4cd59-113">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4cd59-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="4cd59-114">IIS için aşağıdaki örnekte, yayımlanan dosyaya özel üstbilgiyi ekleyin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="4cd59-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="4cd59-115">`web.config`Dosya `bin/Release/{TARGET FRAMEWORK}/publish` klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="4cd59-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="4cd59-116">`web.config`Uygulama klasöre yayımlandığında üzerine yazılmayan IIS için özel bir dosya kullanmak istiyorsanız `publish` , bkz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="4cd59-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="4cd59-117">Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="4cd59-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="4cd59-118">Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="4cd59-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="4cd59-119">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="4cd59-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="4cd59-120"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .</span><span class="sxs-lookup"><span data-stu-id="4cd59-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cd59-121">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4cd59-121">Additional resources</span></span>

* <xref:fundamentals/environments>
