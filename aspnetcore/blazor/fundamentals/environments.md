---
title: ASP.NET Core Blazor ortamları
author: guardrex
description: "' Deki ortamlar Blazor , bir uygulamanın ortamını ayarlama dahil hakkında bilgi edinin Blazor WebAssembly ."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97678294"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="4d077-103">ASP.NET Core Blazor ortamları</span><span class="sxs-lookup"><span data-stu-id="4d077-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="4d077-104">Bu konu için geçerlidir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4d077-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4d077-105">Uygulamalar için kullanılan yaklaşımları açıklayan ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için Blazor Server bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="4d077-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4d077-106">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="4d077-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="4d077-107">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="4d077-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="4d077-108">BlazorBarındırılan bir çözümün istemci tarafı uygulaması ( *`Client`* ), Blazor WebAssembly *`Server`* tarayıcıyı tarayıcıya ileten bir ara yazılım aracılığıyla çözümün uygulamasından bir ortam belirler.</span><span class="sxs-lookup"><span data-stu-id="4d077-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="4d077-109">*`Server`* Uygulama, `blazor-environment` üst bilgi değeri olarak ortama sahip adlı bir üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="4d077-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="4d077-110">*`Client`* Uygulama üstbilgiyi okur.</span><span class="sxs-lookup"><span data-stu-id="4d077-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="4d077-111">*`Server`* Çözüm uygulaması ASP.NET Core bir uygulamadır, bu nedenle ortamın nasıl yapılandırılacağı hakkında daha fazla bilgi içinde bulunur <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="4d077-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4d077-112">Blazor WebAssemblyYerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="4d077-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="4d077-113">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4d077-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="4d077-114">IIS için aşağıdaki örnekte, özel üst bilgi ( `blazor-environment` ) yayımlanan `web.config` dosyaya eklenir.</span><span class="sxs-lookup"><span data-stu-id="4d077-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="4d077-115">`web.config`Dosya `bin/Release/{TARGET FRAMEWORK}/publish` klasöründe bulunur, burada yer tutucu `{TARGET FRAMEWORK}` hedef çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="4d077-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="4d077-116">`web.config`Uygulama klasöre yayımlandığında üzerine yazılmayan IIS için özel bir dosya kullanmak istiyorsanız `publish` , bkz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="4d077-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="4d077-117">Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun.</span><span class="sxs-lookup"><span data-stu-id="4d077-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="4d077-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="4d077-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="4d077-119">Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> ana bilgisayar Oluşturucu kodunda ortama özgü mantık sağlayan özelliği aracılığıyla özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="4d077-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="4d077-120">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4d077-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="4d077-121"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions>Geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin ver aracılığıyla aşağıdaki kullanışlı uzantı yöntemleri verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4d077-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="4d077-122">İçinde `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4d077-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="4d077-123"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .</span><span class="sxs-lookup"><span data-stu-id="4d077-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d077-124">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4d077-124">Additional resources</span></span>

* <xref:fundamentals/environments>
