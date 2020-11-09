---
title: ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme
author: rick-anderson
description: Bağımlılık ekleme kullanarak ASP.NET Core uygulamasına yetkilendirme gereksinimi işleyicilerini nasıl ekleyeceğinizi öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060267"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="e2ff3-103">ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="e2ff3-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="e2ff3-104">[Yetkilendirme işleyicilerinin](xref:security/authorization/policies#handler-registration) , [bağımlılık ekleme](xref:fundamentals/dependency-injection)kullanılarak yapılandırma sırasında hizmet koleksiyonunda kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e2ff3-105">Bir yetkilendirme işleyicisinde değerlendirmek istediğiniz bir kural deposudur olduğunu ve bu deponun hizmet koleksiyonunda kayıtlı olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="e2ff3-106">Yetkilendirme, oluşturucunun içinde çözümlenmek ve bunları çıkarır.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="e2ff3-107">Örneğin, ASP 'yi kullanmak için. NET ' in günlük altyapısı, `ILoggerFactory` işleyiciye ekleme.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="e2ff3-108">Böyle bir işleyici aşağıdaki kod gibi görünebilir:</span><span class="sxs-lookup"><span data-stu-id="e2ff3-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="e2ff3-109">Önceki işleyici, herhangi bir [hizmet ömrüyle](/dotnet/core/extensions/dependency-injection#service-lifetimes)kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e2ff3-110">Aşağıdaki kod, `AddSingleton` önceki işleyiciyi kaydetmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="e2ff3-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="e2ff3-111">Uygulama başlatıldığında işleyicinin bir örneği oluşturulur ve bu Oluşturucu, oluşturucuya kayıtlı öğesini çıkartır `ILoggerFactory` .</span><span class="sxs-lookup"><span data-stu-id="e2ff3-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="e2ff3-112">Entity Framework kullanan işleyiciler tekton olarak kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e2ff3-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
