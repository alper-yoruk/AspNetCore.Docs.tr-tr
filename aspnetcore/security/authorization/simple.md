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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6e37d-103">ASP.NET Core basit yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6e37d-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6e37d-104">MVC 'de yetkilendirme, `AuthorizeAttribute` özniteliği ve çeşitli parametreleri aracılığıyla denetlenir.</span><span class="sxs-lookup"><span data-stu-id="6e37d-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="6e37d-105">En basit noktada, bir denetleyiciye `AuthorizeAttribute` veya eyleme olan erişimi, kimliği doğrulanmış herhangi bir kullanıcı için denetleyiciye veya eyleme uygulama kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="6e37d-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="6e37d-106">Örneğin, aşağıdaki kod kimliği doğrulanmış herhangi bir kullanıcıya erişimi `AccountController` kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="6e37d-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="6e37d-107">Denetleyici yerine bir eyleme yetkilendirme uygulamak istiyorsanız, bu `AuthorizeAttribute` özniteliği eyleme uygulayın:</span><span class="sxs-lookup"><span data-stu-id="6e37d-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="6e37d-108">Artık yalnızca kimliği doğrulanmış kullanıcılar `Logout` işleve erişebilir.</span><span class="sxs-lookup"><span data-stu-id="6e37d-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6e37d-109">Ayrıca, `AllowAnonymous` kimliği doğrulanmamış kullanıcıların tek tek eylemlere erişimine izin vermek için özniteliğini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6e37d-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6e37d-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6e37d-110">For example:</span></span>

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

<span data-ttu-id="6e37d-111">Bu, kimliği doğrulanmış veya kimliği doğrulanmamış/ `AccountController`anonim durumundan bağımsız olarak `Login` herkes tarafından erişilebilen, yalnızca kimliği doğrulanmış kullanıcıların öğesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="6e37d-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6e37d-112">`[AllowAnonymous]`Tüm yetkilendirme deyimlerini atlar.</span><span class="sxs-lookup"><span data-stu-id="6e37d-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6e37d-113">Ve herhangi bir `[AllowAnonymous]` `[Authorize]` özniteliği birleştirirseniz, `[Authorize]` öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="6e37d-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6e37d-114">Örneğin, denetleyici düzeyinde uygularsanız `[AllowAnonymous]` , aynı denetleyicideki (veya `[Authorize]` içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="6e37d-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
