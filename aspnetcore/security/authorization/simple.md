---
title: ASP.NET Core basit yetkilendirme
author: rick-anderson
description: ASP.NET Core denetleyicilerine ve eylemlerine erişimi kısıtlamak için yetkilendir özniteliğini nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 497103a14591476f3167602631b6b011264f5086
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408337"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="c3ca1-103">ASP.NET Core basit yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="c3ca1-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="c3ca1-104">ASP.NET Core yetkilendirme, <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ve çeşitli parametreleriyle denetlenir.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="c3ca1-105">En basit biçiminde, `[Authorize]` özniteliği bir denetleyiciye, eyleme veya Razor sayfaya uygulayarak, bu bileşene erişimi kimliği doğrulanmış herhangi bir kullanıcıyla sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="c3ca1-106">Örneğin, aşağıdaki kod `AccountController` kimliği doğrulanmış herhangi bir kullanıcıya erişimi kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="c3ca1-107">Denetleyici yerine bir eyleme yetkilendirme uygulamak istiyorsanız, bu `AuthorizeAttribute` özniteliği eyleme uygulayın:</span><span class="sxs-lookup"><span data-stu-id="c3ca1-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="c3ca1-108">Artık yalnızca kimliği doğrulanmış kullanıcılar işleve erişebilir `Logout` .</span><span class="sxs-lookup"><span data-stu-id="c3ca1-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="c3ca1-109">Ayrıca, `AllowAnonymous` kimliği doğrulanmamış kullanıcıların tek tek eylemlere erişimine izin vermek için özniteliğini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="c3ca1-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c3ca1-110">For example:</span></span>

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

<span data-ttu-id="c3ca1-111">Bu, `AccountController` `Login` kimliği doğrulanmış veya kimliği doğrulanmamış/anonim durumundan bağımsız olarak herkes tarafından erişilebilen, yalnızca kimliği doğrulanmış kullanıcıların öğesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="c3ca1-112">`[AllowAnonymous]`Tüm yetkilendirme deyimlerini atlar.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="c3ca1-113">`[AllowAnonymous]`Ve herhangi bir özniteliği birleştirirseniz `[Authorize]` , `[Authorize]` öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="c3ca1-114">Örneğin, `[AllowAnonymous]` Denetleyici düzeyinde uygularsanız, `[Authorize]` aynı denetleyicideki (veya içindeki herhangi bir eylemde) tüm öznitelikler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="c3ca1-115">Özniteliği ve Razor sayfaları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="c3ca1-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="c3ca1-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ***not*** Razor Sayfa işleyicilerine uygulanamıyor.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="c3ca1-117">Örneğin,, `[Authorize]` `OnGet` `OnPost` veya diğer sayfa işleyicisine uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="c3ca1-118">Farklı işleyiciler için farklı yetkilendirme gereksinimlerine sahip sayfalar için ASP.NET Core MVC denetleyicisi kullanmayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="c3ca1-119">Aşağıdaki iki yaklaşım, sayfa işleyici yöntemlerine yetkilendirme uygulamak için kullanılabilir Razor :</span><span class="sxs-lookup"><span data-stu-id="c3ca1-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="c3ca1-120">Farklı yetkilendirme gerektiren sayfa işleyicileri için ayrı sayfalar kullanın.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="c3ca1-121">Paylaşılan içeriği bir veya daha fazla [kısmi görünüme](xref:mvc/views/partial)taşıdı.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-121">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="c3ca1-122">Mümkün olduğunda önerilen yaklaşım budur.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="c3ca1-123">Ortak bir sayfa paylaşması gereken içerikler için, [ıasyncpagefilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)öğesinin bir parçası olarak yetkilendirme gerçekleştiren bir filtre yazın.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="c3ca1-124">[Pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub projesinde bu yaklaşım gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c3ca1-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="c3ca1-125">[Authorizepagehandlerfilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) yetkilendirme filtresini uygular:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="c3ca1-125">The [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="c3ca1-126">[[Authorizepagehandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) özniteliği `OnGet` sayfa işleyicisine uygulandı:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="c3ca1-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="c3ca1-127">[Pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) örnek yaklaşımı ***Şu değildir***:</span><span class="sxs-lookup"><span data-stu-id="c3ca1-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="c3ca1-128">Sayfa, sayfa modeli veya küresel olarak, yetkilendirme öznitelikleri ile oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="c3ca1-129">Bir sayfada daha fazla veya örnek varsa, yetkilendirme özniteliklerinin oluşturulması, kimlik doğrulama ve yetkilendirme ile birden çok kez yürütülen sonuçlara neden olur `AuthorizeAttribute` `AuthorizeFilter` .</span><span class="sxs-lookup"><span data-stu-id="c3ca1-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="c3ca1-130">ASP.NET Core kimlik doğrulaması ve yetkilendirme sisteminin geri kalanı ile birlikte çalışın.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="c3ca1-131">Uygulamanız için bu yaklaşımı kullanarak doğru şekilde çalıştığını doğrulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="c3ca1-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="c3ca1-132">`AuthorizeAttribute`On sayfa işleyicilerini desteklemeye yönelik bir plan yoktur Razor .</span><span class="sxs-lookup"><span data-stu-id="c3ca1-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
