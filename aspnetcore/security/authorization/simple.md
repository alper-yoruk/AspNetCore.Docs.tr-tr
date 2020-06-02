---
Başlık: ASP.NET Core Yazar: Rick-Anderson Description içinde basit yetkilendirme: yetkilendirme özniteliğini, erişimi ASP.NET Core denetleyicilerine ve eylemlerine kısıtlamak için nasıl kullanacağınızı öğrenin.
MS. Author: Riande MS. Date: 10/14/2016 No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: güvenlik/yetkilendirme/basit

---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core basit yetkilendirme

<a name="security-authorization-simple"></a>

ASP.NET Core yetkilendirme, <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ve çeşitli parametreleriyle denetlenir. En basit biçiminde, `[Authorize]` özniteliği bir denetleyiciye, eyleme veya Razor sayfaya uygulayarak, bu bileşene erişimi kimliği doğrulanmış herhangi bir kullanıcıyla sınırlandırır.

Örneğin, aşağıdaki kod `AccountController` kimliği doğrulanmış herhangi bir kullanıcıya erişimi kısıtlar.

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

Artık yalnızca kimliği doğrulanmış kullanıcılar işleve erişebilir `Logout` .

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

Bu, `AccountController` `Login` kimliği doğrulanmış veya kimliği doğrulanmamış/anonim durumundan bağımsız olarak herkes tarafından erişilebilen, yalnızca kimliği doğrulanmış kullanıcıların öğesine izin verir.

> [!WARNING]
> `[AllowAnonymous]`Tüm yetkilendirme deyimlerini atlar. `[AllowAnonymous]`Ve herhangi bir özniteliği birleştirirseniz `[Authorize]` , `[Authorize]` öznitelikler yok sayılır. Örneğin, `[AllowAnonymous]` Denetleyici düzeyinde uygularsanız, `[Authorize]` aynı denetleyicideki (veya içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.
