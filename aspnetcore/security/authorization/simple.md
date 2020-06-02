---
<span data-ttu-id="c8203-101">Başlık: ASP.NET Core Yazar: Rick-Anderson Description içinde basit yetkilendirme: yetkilendirme özniteliğini, erişimi ASP.NET Core denetleyicilerine ve eylemlerine kısıtlamak için nasıl kullanacağınızı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="c8203-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="c8203-102">MS. Author: Riande MS. Date: 10/14/2016 No-loc:</span><span class="sxs-lookup"><span data-stu-id="c8203-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="c8203-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8203-103">'Blazor'</span></span>
- <span data-ttu-id="c8203-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8203-104">'Identity'</span></span>
- <span data-ttu-id="c8203-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8203-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8203-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8203-106">'Razor'</span></span>
- <span data-ttu-id="c8203-107">' SignalR ' uid: güvenlik/yetkilendirme/basit</span><span class="sxs-lookup"><span data-stu-id="c8203-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="c8203-108">ASP.NET Core basit yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="c8203-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="c8203-109">ASP.NET Core yetkilendirme, <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ve çeşitli parametreleriyle denetlenir.</span><span class="sxs-lookup"><span data-stu-id="c8203-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="c8203-110">En basit biçiminde, `[Authorize]` özniteliği bir denetleyiciye, eyleme veya Razor sayfaya uygulayarak, bu bileşene erişimi kimliği doğrulanmış herhangi bir kullanıcıyla sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c8203-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="c8203-111">Örneğin, aşağıdaki kod `AccountController` kimliği doğrulanmış herhangi bir kullanıcıya erişimi kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="c8203-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="c8203-112">Denetleyici yerine bir eyleme yetkilendirme uygulamak istiyorsanız, bu `AuthorizeAttribute` özniteliği eyleme uygulayın:</span><span class="sxs-lookup"><span data-stu-id="c8203-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="c8203-113">Artık yalnızca kimliği doğrulanmış kullanıcılar işleve erişebilir `Logout` .</span><span class="sxs-lookup"><span data-stu-id="c8203-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="c8203-114">Ayrıca, `AllowAnonymous` kimliği doğrulanmamış kullanıcıların tek tek eylemlere erişimine izin vermek için özniteliğini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c8203-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="c8203-115">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c8203-115">For example:</span></span>

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

<span data-ttu-id="c8203-116">Bu, `AccountController` `Login` kimliği doğrulanmış veya kimliği doğrulanmamış/anonim durumundan bağımsız olarak herkes tarafından erişilebilen, yalnızca kimliği doğrulanmış kullanıcıların öğesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="c8203-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="c8203-117">`[AllowAnonymous]`Tüm yetkilendirme deyimlerini atlar.</span><span class="sxs-lookup"><span data-stu-id="c8203-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="c8203-118">`[AllowAnonymous]`Ve herhangi bir özniteliği birleştirirseniz `[Authorize]` , `[Authorize]` öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="c8203-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="c8203-119">Örneğin, `[AllowAnonymous]` Denetleyici düzeyinde uygularsanız, `[Authorize]` aynı denetleyicideki (veya içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="c8203-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
