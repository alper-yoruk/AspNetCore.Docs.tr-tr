---
title: ASP.NET Core basit yetkilendirme
author: rick-anderson
description: ASP.NET Core denetleyicilerine ve eylemlerine erişimi kısıtlamak için yetkilendir özniteliğini nasıl kullanacağınızı öğrenin.
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
uid: security/authorization/simple
ms.openlocfilehash: ae8fb47e58924d559f1c2c4ed7c9545c37141209
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061346"
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
> `[AllowAnonymous]` Tüm yetkilendirme deyimlerini atlar. `[AllowAnonymous]`Ve herhangi bir özniteliği birleştirirseniz `[Authorize]` , `[Authorize]` öznitelikler yok sayılır. Örneğin, `[AllowAnonymous]` Denetleyici düzeyinde uygularsanız, `[Authorize]` aynı denetleyicideki (veya içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a>Özniteliği ve Razor sayfaları yetkilendir

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>Sayfa işleyicilerine * **Not** \ ' a uygulanamaz Razor . Örneğin,, `[Authorize]` `OnGet` `OnPost` veya diğer sayfa işleyicisine uygulanamaz. Farklı işleyiciler için farklı yetkilendirme gereksinimlerine sahip sayfalar için ASP.NET Core MVC denetleyicisi kullanmayı göz önünde bulundurun.

Aşağıdaki iki yaklaşım, sayfa işleyici yöntemlerine yetkilendirme uygulamak için kullanılabilir Razor :

_ Farklı yetkilendirme gerektiren sayfa işleyicileri için ayrı sayfalar kullanın. Paylaşılan içeriği bir veya daha fazla [kısmi görünüme](xref:mvc/views/partial)taşıdı. Mümkün olduğunda önerilen yaklaşım budur.
* Ortak bir sayfa paylaşması gereken içerikler için, [ıasyncpagefilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)öğesinin bir parçası olarak yetkilendirme gerçekleştiren bir filtre yazın. [Pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub projesinde bu yaklaşım gösterilmektedir:
  * [Authorizeındexpagehandlerfilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) yetkilendirme filtresini uygular:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * [[Authorizepagehandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) özniteliği `OnGet` sayfa işleyicisine uygulandı:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> [Pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) örnek yaklaşımı, sayfa, sayfa modeli veya küresel olarak bir yetkilendirme özniteliği uygulanmış * **değil** _: _ Compose. Bir sayfada daha fazla veya örnek varsa, yetkilendirme özniteliklerinin oluşturulması, kimlik doğrulama ve yetkilendirme ile birden çok kez yürütülen sonuçlara neden olur `AuthorizeAttribute` `AuthorizeFilter` .
> * ASP.NET Core kimlik doğrulaması ve yetkilendirme sisteminin geri kalanı ile birlikte çalışın. Uygulamanız için bu yaklaşımı kullanarak doğru şekilde çalıştığını doğrulamanız gerekir.

`AuthorizeAttribute`On sayfa işleyicilerini desteklemeye yönelik bir plan yoktur Razor . 
