---
title: ASP.NET Core basit yetkilendirme
author: rick-anderson
description: ASP.NET Core denetleyicilerine ve eylemlerine erişimi kısıtlamak için yetkilendir özniteliğini nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775641"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core basit yetkilendirme

<a name="security-authorization-simple"></a>

MVC 'de yetkilendirme, `AuthorizeAttribute` özniteliği ve çeşitli parametreleri aracılığıyla denetlenir. En basit noktada, bir denetleyiciye `AuthorizeAttribute` veya eyleme olan erişimi, kimliği doğrulanmış herhangi bir kullanıcı için denetleyiciye veya eyleme uygulama kısıtlar.

Örneğin, aşağıdaki kod kimliği doğrulanmış herhangi bir kullanıcıya erişimi `AccountController` kısıtlar.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Denetleyici yerine bir eyleme yetkilendirme uygulamak istiyorsanız, bu `AuthorizeAttribute` özniteliği eyleme uygulayın:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Artık yalnızca kimliği doğrulanmış kullanıcılar `Logout` işleve erişebilir.

Ayrıca, `AllowAnonymous` kimliği doğrulanmamış kullanıcıların tek tek eylemlere erişimine izin vermek için özniteliğini de kullanabilirsiniz. Örneğin:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Bu, kimliği doğrulanmış veya kimliği doğrulanmamış/ `AccountController`anonim durumundan bağımsız olarak `Login` herkes tarafından erişilebilen, yalnızca kimliği doğrulanmış kullanıcıların öğesine izin verir.

> [!WARNING]
> `[AllowAnonymous]`Tüm yetkilendirme deyimlerini atlar. Ve herhangi bir `[AllowAnonymous]` `[Authorize]` özniteliği birleştirirseniz, `[Authorize]` öznitelikler yok sayılır. Örneğin, denetleyici düzeyinde uygularsanız `[AllowAnonymous]` , aynı denetleyicideki (veya `[Authorize]` içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.
