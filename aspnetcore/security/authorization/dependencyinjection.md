---
title: ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme
author: rick-anderson
description: Bağımlılık ekleme kullanarak ASP.NET Core uygulamasına yetkilendirme gereksinimi işleyicilerini nasıl ekleyeceğinizi öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775126"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="7bccb-103">ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="7bccb-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="7bccb-104">[Yetkilendirme işleyicilerinin](xref:security/authorization/policies#handler-registration) yapılandırma sırasında hizmet koleksiyonunda kayıtlı olması gerekir ( [bağımlılık ekleme](xref:fundamentals/dependency-injection)kullanılarak).</span><span class="sxs-lookup"><span data-stu-id="7bccb-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="7bccb-105">Bir yetkilendirme işleyicisinde değerlendirmek istediğiniz bir kural deposudur olduğunu ve bu deponun hizmet koleksiyonunda kayıtlı olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="7bccb-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="7bccb-106">Yetkilendirme çözülecek ve oluşturucuya eklenecektir.</span><span class="sxs-lookup"><span data-stu-id="7bccb-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="7bccb-107">Örneğin, ASP 'yi kullanmak isterseniz. İşleyicinizden eklemek istediğiniz NET 'in günlük altyapısı `ILoggerFactory` .</span><span class="sxs-lookup"><span data-stu-id="7bccb-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="7bccb-108">Böyle bir işleyici şöyle görünebilir:</span><span class="sxs-lookup"><span data-stu-id="7bccb-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="7bccb-109">İşleyiciyi şu ile `services.AddSingleton()`kaydedersiniz:</span><span class="sxs-lookup"><span data-stu-id="7bccb-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="7bccb-110">Uygulamanız başlatıldığında işleyicinin bir örneği oluşturulacaktır ve dı, oluşturucuya kayıtlı `ILoggerFactory` olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="7bccb-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="7bccb-111">Entity Framework kullanan işleyiciler tekton olarak kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="7bccb-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
