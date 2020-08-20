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
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="20065-103">ASP.NET Core rol tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="20065-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="20065-104">Bir kimlik oluşturulduğunda, bir veya daha fazla role ait olabilir.</span><span class="sxs-lookup"><span data-stu-id="20065-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="20065-105">Örneğin, Tracy yönetici 'ye ait olabilir ve IHOST Scott yalnızca kullanıcı rolüne ait olabilir.</span><span class="sxs-lookup"><span data-stu-id="20065-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="20065-106">Bu rollerin oluşturulması ve yönetilmesi, yetkilendirme işleminin yedekleme deposuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="20065-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="20065-107">Roller, [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) sınıfındaki [ıınrole](/dotnet/api/system.security.principal.genericprincipal.isinrole) yöntemi aracılığıyla geliştiriciye sunulur.</span><span class="sxs-lookup"><span data-stu-id="20065-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="20065-108">Rol denetimleri ekleme</span><span class="sxs-lookup"><span data-stu-id="20065-108">Adding role checks</span></span>

<span data-ttu-id="20065-109">Rol tabanlı yetkilendirme denetimleri bildirime dayalı olarak, &mdash; Geliştirici bunları kendi kodlarında bir denetleyiciye veya denetleyici içindeki bir eyleme göre katıştırır ve geçerli kullanıcının istenen kaynağa erişmek için üyesi olması gereken rolleri belirterek.</span><span class="sxs-lookup"><span data-stu-id="20065-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="20065-110">Örneğin, aşağıdaki kod, ' deki tüm eylemlere erişimi `AdministrationController` rolün üyesi olan kullanıcılara kısıtlar `Administrator` :</span><span class="sxs-lookup"><span data-stu-id="20065-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="20065-111">Birden çok rolü, virgülle ayrılmış bir liste olarak belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="20065-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="20065-112">Bu denetleyiciye yalnızca rolün üyesi veya rolü olan kullanıcılar erişebilir `HRManager` `Finance` .</span><span class="sxs-lookup"><span data-stu-id="20065-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="20065-113">Birden çok öznitelik uygularsanız, bir erişen kullanıcının belirtilen tüm rollerin üyesi olması gerekir; Aşağıdaki örnek, bir kullanıcının hem hem de rolünün bir üyesi olması gerekir `PowerUser` `ControlPanelUser` .</span><span class="sxs-lookup"><span data-stu-id="20065-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="20065-114">İşlem düzeyinde ek rol yetkilendirme öznitelikleri uygulayarak erişimi daha da sınırlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="20065-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="20065-115">Rolün önceki kod parçacığı üyelerinde `Administrator` veya `PowerUser` rol denetleyiciye ve `SetTime` eyleme erişebilir, ancak yalnızca `Administrator` rolün üyeleri `ShutDown` eyleme erişebilir.</span><span class="sxs-lookup"><span data-stu-id="20065-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="20065-116">Ayrıca, bir denetleyiciyi kilitleyebilir, ancak tek tek eylemlere anonim, kimliği doğrulanmamış erişime izin verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20065-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<span data-ttu-id="20065-117">Sayfalar için, şu Razor `AuthorizeAttribute` şekilde uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="20065-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="20065-118">Bir [kural](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)kullanma veya</span><span class="sxs-lookup"><span data-stu-id="20065-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="20065-119">Örneğe uygulanıyor `AuthorizeAttribute` `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="20065-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

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
> <span data-ttu-id="20065-120">Dahil filtre öznitelikleri, `AuthorizeAttribute` yalnızca PageModel 'e uygulanabilir ve belirli sayfa işleyici yöntemlerine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="20065-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="20065-121">İlke tabanlı rol denetimleri</span><span class="sxs-lookup"><span data-stu-id="20065-121">Policy based role checks</span></span>

<span data-ttu-id="20065-122">Rol gereksinimleri, bir geliştiricinin bir ilkeyi yetkilendirme hizmeti yapılandırmasının bir parçası olarak bir ilke kaydettiğinde yeni Ilke sözdizimi kullanılarak da ifade edilebilir.</span><span class="sxs-lookup"><span data-stu-id="20065-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="20065-123">Bu, normal olarak `ConfigureServices()` *Startup.cs* dosyanızda oluşur.</span><span class="sxs-lookup"><span data-stu-id="20065-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="20065-124">İlkeler `Policy` , özniteliğinde özelliği kullanılarak uygulanır `AuthorizeAttribute` :</span><span class="sxs-lookup"><span data-stu-id="20065-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="20065-125">Bir gereksinimde birden fazla izin verilen rol belirtmek istiyorsanız, bunları yönteme parametre olarak belirtebilirsiniz `RequireRole` :</span><span class="sxs-lookup"><span data-stu-id="20065-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="20065-126">Bu örnek `Administrator` , veya rollerine ait olan kullanıcıları yetkilendirir `PowerUser` `BackupAdministrator` .</span><span class="sxs-lookup"><span data-stu-id="20065-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="20065-127">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="20065-127">Add Role services to Identity</span></span>

<span data-ttu-id="20065-128">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="20065-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

