---
title: ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme
author: rick-anderson
description: Bağımlılık ekleme kullanarak ASP.NET Core uygulamasına yetkilendirme gereksinimi işleyicilerini nasıl ekleyeceğinizi öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021009"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core 'de gereksinim işleyicilerde bağımlılık ekleme

<a name="security-authorization-di"></a>

[Yetkilendirme işleyicilerinin](xref:security/authorization/policies#handler-registration) yapılandırma sırasında hizmet koleksiyonunda kayıtlı olması gerekir ( [bağımlılık ekleme](xref:fundamentals/dependency-injection)kullanılarak).

Bir yetkilendirme işleyicisinde değerlendirmek istediğiniz bir kural deposudur olduğunu ve bu deponun hizmet koleksiyonunda kayıtlı olduğunu varsayalım. Yetkilendirme çözülecek ve oluşturucuya eklenecektir.

Örneğin, ASP 'yi kullanmak isterseniz. İşleyicinizden eklemek istediğiniz NET 'in günlük altyapısı `ILoggerFactory` . Böyle bir işleyici şöyle görünebilir:

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

İşleyiciyi şu ile kaydedersiniz `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Uygulamanız başlatıldığında işleyicinin bir örneği oluşturulacaktır ve dı, oluşturucuya kayıtlı olarak eklenir `ILoggerFactory` .

> [!NOTE]
> Entity Framework kullanan işleyiciler tekton olarak kaydedilmelidir.
