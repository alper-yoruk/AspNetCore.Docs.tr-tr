---
title: ASP.NET Core rol tabanlı yetkilendirme
author: rick-anderson
description: Rolleri Yetkilendir özniteliğine geçirerek ASP.NET Core denetleyicisi ve eylem erişimini nasıl kısıtlayacağınızı öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/authorization/roles
ms.openlocfilehash: 7673bb006c344e6f9baaa3cd99c4bdb4a6fc2862
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635183"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core rol tabanlı yetkilendirme

<a name="security-authorization-role-based"></a>

Bir kimlik oluşturulduğunda, bir veya daha fazla role ait olabilir. Örneğin, Tracy yönetici 'ye ait olabilir ve IHOST Scott yalnızca kullanıcı rolüne ait olabilir. Bu rollerin oluşturulması ve yönetilmesi, yetkilendirme işleminin yedekleme deposuna bağlıdır. Roller, [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) sınıfındaki [ıınrole](/dotnet/api/system.security.principal.genericprincipal.isinrole) yöntemi aracılığıyla geliştiriciye sunulur.

## <a name="adding-role-checks"></a>Rol denetimleri ekleme

Rol tabanlı yetkilendirme denetimleri bildirime dayalı olarak, &mdash; Geliştirici bunları kendi kodlarında bir denetleyiciye veya denetleyici içindeki bir eyleme göre katıştırır ve geçerli kullanıcının istenen kaynağa erişmek için üyesi olması gereken rolleri belirterek.

Örneğin, aşağıdaki kod, ' deki tüm eylemlere erişimi `AdministrationController` rolün üyesi olan kullanıcılara kısıtlar `Administrator` :

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

Bu denetleyiciye yalnızca rolün üyesi veya rolü olan kullanıcılar erişebilir `HRManager` `Finance` .

Birden çok öznitelik uygularsanız, bir erişen kullanıcının belirtilen tüm rollerin üyesi olması gerekir; Aşağıdaki örnek, bir kullanıcının hem hem de rolünün bir üyesi olması gerekir `PowerUser` `ControlPanelUser` .

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

Rolün önceki kod parçacığı üyelerinde `Administrator` veya `PowerUser` rol denetleyiciye ve `SetTime` eyleme erişebilir, ancak yalnızca `Administrator` rolün üyeleri `ShutDown` eyleme erişebilir.

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

Sayfalar için, şu Razor `AuthorizeAttribute` şekilde uygulanabilir:

* Bir [kural](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)kullanma veya
* Örneğe uygulanıyor `AuthorizeAttribute` `PageModel` :

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
> Dahil filtre öznitelikleri, `AuthorizeAttribute` yalnızca PageModel 'e uygulanabilir ve belirli sayfa işleyici yöntemlerine uygulanamaz.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>İlke tabanlı rol denetimleri

Rol gereksinimleri, bir geliştiricinin bir ilkeyi yetkilendirme hizmeti yapılandırmasının bir parçası olarak bir ilke kaydettiğinde yeni Ilke sözdizimi kullanılarak da ifade edilebilir. Bu, normal olarak `ConfigureServices()` *Startup.cs* dosyanızda oluşur.

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

İlkeler `Policy` , özniteliğinde özelliği kullanılarak uygulanır `AuthorizeAttribute` :

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Bir gereksinimde birden fazla izin verilen rol belirtmek istiyorsanız, bunları yönteme parametre olarak belirtebilirsiniz `RequireRole` :

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Bu örnek `Administrator` , veya rollerine ait olan kullanıcıları yetkilendirir `PowerUser` `BackupAdministrator` .

### <a name="add-role-services-to-no-locidentity"></a>Rol hizmetlerini Ekle Identity

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

