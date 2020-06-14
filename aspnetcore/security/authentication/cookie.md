---
title: ASP.NET Core olmadan tanımlama bilgisi kimlik doğrulaması kullanmaIdentity
author: rick-anderson
description: ASP.NET Core olmadan tanımlama bilgisi kimlik doğrulamasını nasıl kullanacağınızı öğrenin Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 7d2f338f8ece6bd3cc99d5f2ab8153b5c465c7a4
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724245"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f2770-103">ASP.NET Core olmadan tanımlama bilgisi kimlik doğrulaması kullanmaIdentity</span><span class="sxs-lookup"><span data-stu-id="f2770-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="f2770-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2770-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2770-105">ASP.NET Core Identity , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f2770-106">Ancak, ASP.NET Core olmayan bir tanımlama bilgisi tabanlı kimlik doğrulama sağlayıcısı Identity kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f2770-107">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="f2770-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f2770-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2770-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f2770-109">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f2770-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f2770-110">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f2770-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f2770-111">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f2770-112">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f2770-113">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f2770-113">Configuration</span></span>

<span data-ttu-id="f2770-114">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f2770-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f2770-115">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetlerini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="f2770-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f2770-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f2770-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f2770-117">`AuthenticationScheme`birden fazla tanımlama bilgisi kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f2770-118">`AuthenticationScheme`' In [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, şema Için bir "tanımlama bilgileri" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f2770-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f2770-119">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f2770-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f2770-120">Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f2770-121">İçin bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmazsa <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") kullanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f2770-122">Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği `true` Varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f2770-123">Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f2770-124">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="f2770-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f2770-125">İçinde `Startup.Configure` , `UseAuthentication` `UseAuthorization` özelliği ayarlamak ve `HttpContext.User` Istekler için yetkilendirme ara yazılımını çalıştırmak için öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f2770-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="f2770-126">`UseAuthentication` `UseAuthorization` Çağrılmadan önce ve yöntemlerini çağırın `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="f2770-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f2770-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f2770-128">`CookieAuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2770-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="f2770-129">Tanımlama bilgisi Ilkesi ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="f2770-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="f2770-130">[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar.</span><span class="sxs-lookup"><span data-stu-id="f2770-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f2770-131">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="f2770-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f2770-132">Tanımlama bilgisi <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> işlemenin genel özelliklerini denetlemek için tanımlama bilgisi Ilkesi ara yazılımı, tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="f2770-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f2770-133">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f2770-134">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="f2770-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f2770-135">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="f2770-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f2770-136">İçin tanımlama bilgisi Ilkesi ara yazılımı ayarı, `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` aşağıdaki matriye göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f2770-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f2770-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f2770-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="f2770-138">Cookie.SameSite</span></span> | <span data-ttu-id="f2770-139">Sonuç tanımlama bilgisi. SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="f2770-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f2770-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-140">SameSiteMode.None</span></span>     | <span data-ttu-id="f2770-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-141">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-144">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2770-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f2770-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-148">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2770-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f2770-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-155">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2770-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2770-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="f2770-161">Kimlik doğrulama tanımlama bilgisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f2770-161">Create an authentication cookie</span></span>

<span data-ttu-id="f2770-162">Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="f2770-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f2770-163">Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f2770-164"><xref:System.Security.Claims.ClaimsIdentity>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="f2770-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="f2770-165">`SignInAsync`şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="f2770-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f2770-166">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f2770-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>, varsayılan olarak yalnızca birkaç belirli yolda kullanılır. Örneğin, oturum açma yolu ve oturum kapatma yolları.</span><span class="sxs-lookup"><span data-stu-id="f2770-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="f2770-168">Daha fazla bilgi için bkz. [tanımlama, ıeauthenticationhandler kaynağı](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="f2770-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="f2770-169">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f2770-170">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f2770-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f2770-171">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="f2770-171">Sign out</span></span>

<span data-ttu-id="f2770-172">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="f2770-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f2770-173">`CookieAuthenticationDefaults.AuthenticationScheme`(Veya "Cookies"), düzen olarak (örneğin, "ContosoCookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f2770-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f2770-174">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f2770-175">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="f2770-175">React to back-end changes</span></span>

<span data-ttu-id="f2770-176">Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f2770-177">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="f2770-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f2770-178">Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.</span><span class="sxs-lookup"><span data-stu-id="f2770-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f2770-179">Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="f2770-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f2770-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Olay, tanımlama bilgisi kimliği doğrulamasını ele almak ve geçersiz kılmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f2770-181">Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="f2770-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f2770-182">Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f2770-183">Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f2770-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f2770-184">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="f2770-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f2770-185">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f2770-186">Veritabanı değere göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için `LastChanged` , `LastChanged` veritabanından geçerli değeri içeren bir talep ile tanımlama bilgisini oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="f2770-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="f2770-187">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="f2770-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f2770-188">Aşağıda örnek bir uygulama verilmiştir `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="f2770-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="f2770-189">Yöntemine tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f2770-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f2770-190">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `CustomCookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="f2770-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f2770-191">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="f2770-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f2770-192">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="f2770-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f2770-193">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="f2770-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f2770-194">Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="f2770-195">Kalıcı tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="f2770-195">Persistent cookies</span></span>

<span data-ttu-id="f2770-196">Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f2770-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f2770-197">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f2770-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f2770-198">Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f2770-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f2770-199">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f2770-200">Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.</span><span class="sxs-lookup"><span data-stu-id="f2770-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f2770-201">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="f2770-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="f2770-202">Mutlak tanımlama bilgisi süre sonu</span><span class="sxs-lookup"><span data-stu-id="f2770-202">Absolute cookie expiration</span></span>

<span data-ttu-id="f2770-203">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="f2770-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f2770-204">Kalıcı tanımlama bilgisi oluşturmak için `IsPersistent` de ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f2770-205">Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f2770-206">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="f2770-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f2770-207">Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f2770-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f2770-208">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="f2770-208">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2770-209">ASP.NET Core Identity , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f2770-210">Ancak, ASP.NET Core olmayan tanımlama bilgisi tabanlı kimlik doğrulama sağlayıcısı Identity kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f2770-211">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="f2770-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f2770-212">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2770-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f2770-213">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f2770-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f2770-214">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f2770-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f2770-215">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f2770-216">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f2770-217">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f2770-217">Configuration</span></span>

<span data-ttu-id="f2770-218">Uygulama [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi için proje dosyasında bir paket başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f2770-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f2770-219">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="f2770-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f2770-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f2770-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f2770-221">`AuthenticationScheme`birden fazla tanımlama bilgisi kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f2770-222">`AuthenticationScheme`' In [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, şema Için bir "tanımlama bilgileri" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f2770-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f2770-223">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f2770-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f2770-224">Uygulamanın kimlik doğrulama düzeni, uygulamanın tanımlama bilgisi kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f2770-225">İçin bir tanımlama bilgisi kimlik doğrulama düzeni sağlanmazsa <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies") kullanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f2770-226">Kimlik doğrulama tanımlama bilgisinin <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği `true` Varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f2770-227">Bir site ziyaretçisi veri toplamaya onay vermemişse kimlik doğrulama tanımlama bilgilerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f2770-228">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="f2770-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f2770-229">`Startup.Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="f2770-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f2770-230">`UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="f2770-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f2770-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f2770-232">`CookieAuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2770-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="f2770-233">Tanımlama bilgisi Ilkesi ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="f2770-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="f2770-234">[Tanımlama bilgisi Ilkesi ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , tanımlama bilgisi İlkesi yeteneklerini sunar.</span><span class="sxs-lookup"><span data-stu-id="f2770-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f2770-235">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="f2770-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f2770-236">Tanımlama bilgisi <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> işlemenin genel özelliklerini denetlemek için tanımlama bilgisi Ilkesi ara yazılımı, tanımlama bilgileri eklenmiş veya silinmiş olduğunda tanımlama bilgisi işleme işleyicilerine kanca olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="f2770-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f2770-237">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f2770-238">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="f2770-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f2770-239">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için tanımlama bilgisi güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="f2770-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f2770-240">İçin tanımlama bilgisi Ilkesi ara yazılımı ayarı, `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` aşağıdaki matriye göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f2770-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f2770-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f2770-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="f2770-242">Cookie.SameSite</span></span> | <span data-ttu-id="f2770-243">Sonuç tanımlama bilgisi. SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="f2770-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f2770-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-244">SameSiteMode.None</span></span>     | <span data-ttu-id="f2770-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-245">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-248">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2770-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f2770-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-252">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2770-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f2770-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="f2770-259">SameSiteMode.None</span></span><br><span data-ttu-id="f2770-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="f2770-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2770-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2770-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2770-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2770-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="f2770-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="f2770-265">Kimlik doğrulama tanımlama bilgisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f2770-265">Create an authentication cookie</span></span>

<span data-ttu-id="f2770-266">Kullanıcı bilgilerini tutan bir tanımlama bilgisi oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="f2770-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f2770-267">Kullanıcı bilgileri serileştirilir ve tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f2770-268"><xref:System.Security.Claims.ClaimsIdentity>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="f2770-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="f2770-269">`SignInAsync`şifreli bir tanımlama bilgisi oluşturur ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="f2770-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f2770-270">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f2770-271">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f2770-272">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f2770-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f2770-273">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="f2770-273">Sign out</span></span>

<span data-ttu-id="f2770-274">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="f2770-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f2770-275">`CookieAuthenticationDefaults.AuthenticationScheme`(Veya "Cookies"), düzen olarak (örneğin, "ContosoCookie") kullanılmazsa, kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f2770-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f2770-276">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f2770-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f2770-277">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="f2770-277">React to back-end changes</span></span>

<span data-ttu-id="f2770-278">Tanımlama bilgisi oluşturulduktan sonra tanımlama bilgisi tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f2770-279">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="f2770-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f2770-280">Uygulamanın tanımlama bilgisi kimlik doğrulama sistemi, kimlik doğrulama tanımlama bilgisine göre istekleri işlemeye devam eder.</span><span class="sxs-lookup"><span data-stu-id="f2770-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f2770-281">Kimlik doğrulama tanımlama bilgisi geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır.</span><span class="sxs-lookup"><span data-stu-id="f2770-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f2770-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Olay, tanımlama bilgisi kimliği doğrulamasını ele almak ve geçersiz kılmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f2770-283">Her istekte tanımlama bilgisinin doğrulanması, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="f2770-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f2770-284">Tanımlama bilgisi doğrulamasına yönelik bir yaklaşım, kullanıcı veritabanının değiştiği zaman izlemenin izlenmesine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f2770-285">Kullanıcının tanımlama bilgisi verildikten sonra veritabanı değiştirilmediyse, tanımlama bilgisi hala geçerliyse kullanıcının kimliğini yeniden doğrulamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="f2770-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f2770-286">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="f2770-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f2770-287">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f2770-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f2770-288">Veritabanı değere göre değiştiğinde bir tanımlama bilgisini geçersiz kılmak için `LastChanged` , `LastChanged` veritabanından geçerli değeri içeren bir talep ile tanımlama bilgisini oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="f2770-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="f2770-289">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="f2770-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f2770-290">Aşağıda örnek bir uygulama verilmiştir `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="f2770-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="f2770-291">Yöntemine tanımlama bilgisi hizmeti kaydı sırasında olay örneğini kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f2770-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f2770-292">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `CustomCookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="f2770-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f2770-293">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="f2770-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f2770-294">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="f2770-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f2770-295">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="f2770-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f2770-296">Her istekteki tüm kullanıcılar için kimlik doğrulama tanımlama bilgilerinin doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="f2770-297">Kalıcı tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="f2770-297">Persistent cookies</span></span>

<span data-ttu-id="f2770-298">Tanımlama bilgisinin tarayıcı oturumları arasında kalıcı olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f2770-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f2770-299">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f2770-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f2770-300">Aşağıdaki kod parçacığı, tarayıcı kapanışları aracılığıyla ilerlikli bir kimlik ve ilgili tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f2770-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f2770-301">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f2770-302">Tarayıcı kapalıyken tanımlama bilgisinin süresi dolarsa tarayıcı, yeniden başlatıldıktan sonra tanımlama bilgisini temizler.</span><span class="sxs-lookup"><span data-stu-id="f2770-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f2770-303">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="f2770-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="f2770-304">Mutlak tanımlama bilgisi süre sonu</span><span class="sxs-lookup"><span data-stu-id="f2770-304">Absolute cookie expiration</span></span>

<span data-ttu-id="f2770-305">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="f2770-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f2770-306">Kalıcı tanımlama bilgisi oluşturmak için `IsPersistent` de ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f2770-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f2770-307">Aksi takdirde, tanımlama bilgisi oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="f2770-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f2770-308">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="f2770-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f2770-309">Aşağıdaki kod parçacığı, 20 dakika boyunca bir kimlik ve karşılık gelen tanımlama bilgisi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f2770-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f2770-310">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="f2770-310">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f2770-311">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f2770-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="f2770-312">İlke tabanlı rol denetimleri</span><span class="sxs-lookup"><span data-stu-id="f2770-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
