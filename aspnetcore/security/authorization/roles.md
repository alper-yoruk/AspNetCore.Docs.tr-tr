---
title: ASP.NET Core rol tabanlı yetkilendirme
author: rick-anderson
description: Rolleri Yetkilendir özniteliğine geçirerek ASP.NET Core denetleyicisi ve eylem erişimini nasıl kısıtlayacağınızı öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 01d4239377b128f711a110a821e1afea58ca14a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776545"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core rol tabanlı yetkilendirme

<a name="security-authorization-role-based"></a>

Bir kimlik oluşturulduğunda, bir veya daha fazla role ait olabilir. Örneğin, Tracy yönetici 'ye ait olabilir ve IHOST Scott yalnızca kullanıcı rolüne ait olabilir. Bu rollerin oluşturulması ve yönetilmesi, yetkilendirme işleminin yedekleme deposuna bağlıdır. Roller, [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) sınıfındaki [ıınrole](/dotnet/api/system.security.principal.genericprincipal.isinrole) yöntemi aracılığıyla geliştiriciye sunulur.

## <a name="adding-role-checks"></a>Rol denetimleri ekleme

Rol tabanlı yetkilendirme denetimleri bildirime&mdash;dayalı olarak, geliştirici bunları kendi kodlarında bir denetleyiciye veya denetleyici içindeki bir eyleme göre katıştırır ve geçerli kullanıcının istenen kaynağa erişmek için üyesi olması gereken rolleri belirterek.

Örneğin, aşağıdaki kod, `AdministrationController` ' deki tüm eylemlere erişimi `Administrator` rolün üyesi olan kullanıcılara kısıtlar:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Birden çok rolü, virgülle ayrılmış bir liste olarak belirtebilirsiniz:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Bu denetleyiciye yalnızca `HRManager` rolün üyesi veya `Finance` rolü olan kullanıcılar erişebilir.

Birden çok öznitelik uygularsanız, bir erişen kullanıcının belirtilen tüm rollerin üyesi olması gerekir; Aşağıdaki örnek, bir kullanıcının hem hem de `PowerUser` `ControlPanelUser` rolünün bir üyesi olması gerekir.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

İşlem düzeyinde ek rol yetkilendirme öznitelikleri uygulayarak erişimi daha da sınırlayabilirsiniz:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

`Administrator` Rolün önceki kod `PowerUser` parçacığı üyelerinde veya rol denetleyiciye ve `SetTime` eyleme erişebilir, ancak yalnızca `Administrator` rolün üyeleri `ShutDown` eyleme erişebilir.

Ayrıca, bir denetleyiciyi kilitleyebilir, ancak tek tek eylemlere anonim, kimliği doğrulanmamış erişime izin verebilirsiniz.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Sayfalar Razor için, şu `AuthorizeAttribute` şekilde uygulanabilir:

* Bir [kural](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)kullanma veya
* `AuthorizeAttribute` `PageModel`

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Dahil `AuthorizeAttribute`filtre öznitelikleri, yalnızca pagemodel 'e uygulanabilir ve belirli sayfa işleyici yöntemlerine uygulanamaz.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>İlke tabanlı rol denetimleri

Rol gereksinimleri, bir geliştiricinin bir ilkeyi yetkilendirme hizmeti yapılandırmasının bir parçası olarak bir ilke kaydettiğinde yeni Ilke sözdizimi kullanılarak da ifade edilebilir. Bu, `ConfigureServices()` normal olarak *Startup.cs* dosyanızda oluşur.

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

İlkeler, `Policy` `AuthorizeAttribute` özniteliğinde özelliği kullanılarak uygulanır:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Bir gereksinimde birden fazla izin verilen rol belirtmek istiyorsanız, `RequireRole` bunları yönteme parametre olarak belirtebilirsiniz:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Bu örnek `Administrator`, `PowerUser` veya `BackupAdministrator` rollerine ait olan kullanıcıları yetkilendirir.

### <a name="add-role-services-to-identity"></a>Rol hizmetlerini EkleIdentity

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

