---
title: ASP.NET Core Blazor ortamları
author: guardrex
description: İçindeki ortamlar hakkında bilgi edinmek Blazor için, Blazor webassembly uygulamasının ortamını ayarlama dahil.
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 203f29ce606a313463e416b068177ce02acd6231
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103841"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="d9274-103">ASP.NET Core Blazor ortamları</span><span class="sxs-lookup"><span data-stu-id="d9274-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="d9274-104">Bu konu, Blazor webassembly için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d9274-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="d9274-105">ASP.NET Core uygulama yapılandırması hakkında genel yönergeler için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="d9274-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d9274-106">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="d9274-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d9274-107">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="d9274-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="d9274-108">Barındırılan Blazor webassembly uygulaması, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan alır `blazor-environment` .</span><span class="sxs-lookup"><span data-stu-id="d9274-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="d9274-109">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="d9274-109">The value of the header is the environment.</span></span> <span data-ttu-id="d9274-110">Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="d9274-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="d9274-111">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="d9274-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d9274-112">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="d9274-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="d9274-113">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9274-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="d9274-114">IIS için aşağıdaki örnekte, yayımlanan *web.config* dosyasına özel üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d9274-114">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="d9274-115">*web.config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="d9274-115">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="d9274-116">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel bir *web.config* dosyası kullanmak için, bkz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="d9274-116">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="d9274-117">Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="d9274-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="d9274-118">Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="d9274-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="d9274-119">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="d9274-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="d9274-120"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .</span><span class="sxs-lookup"><span data-stu-id="d9274-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9274-121">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d9274-121">Additional resources</span></span>

* <xref:fundamentals/environments>
