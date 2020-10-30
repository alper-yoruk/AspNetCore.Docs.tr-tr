---
title: ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme
author: rick-anderson
description: Bağımlılık ekleme kullanarak ASP.NET Core uygulamasına yetkilendirme gereksinimi işleyicilerini nasıl ekleyeceğinizi öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060267"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme

<a name="security-authorization-di"></a>

[Yetkilendirme işleyicilerinin](xref:security/authorization/policies#handler-registration) , [bağımlılık ekleme](xref:fundamentals/dependency-injection)kullanılarak yapılandırma sırasında hizmet koleksiyonunda kayıtlı olması gerekir.

Bir yetkilendirme işleyicisinde değerlendirmek istediğiniz bir kural deposudur olduğunu ve bu deponun hizmet koleksiyonunda kayıtlı olduğunu varsayalım. Yetkilendirme, oluşturucunun içinde çözümlenmek ve bunları çıkarır.

Örneğin, ASP 'yi kullanmak için. NET ' in günlük altyapısı, `ILoggerFactory` işleyiciye ekleme. Böyle bir işleyici aşağıdaki kod gibi görünebilir:

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

Önceki işleyici, herhangi bir [hizmet ömrüyle](/dotnet/core/extensions/dependency-injection#service-lifetimes)kaydedilebilir. Aşağıdaki kod, `AddSingleton` önceki işleyiciyi kaydetmek için kullanır:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Uygulama başlatıldığında işleyicinin bir örneği oluşturulur ve bu Oluşturucu, oluşturucuya kayıtlı öğesini çıkartır `ILoggerFactory` .

> [!NOTE]
> Entity Framework kullanan işleyiciler tekton olarak kaydedilmelidir.
