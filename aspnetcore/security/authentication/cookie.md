---
title: cookieKimlik doğrulamasını kullanmadan kullanınASP.NET Core Identity
author: rick-anderson
description: cookieKimlik doğrulamasını olmadan nasıl kullanacağınızı öğrenin ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 211b6c7ec0bc7a48671e614427961cb332d06aa3
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671775"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="2460d-103">cookieKimlik doğrulamasını kullanmadan kullanınASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2460d-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="2460d-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2460d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2460d-105">ASP.NET Core Identity , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2460d-106">Ancak, cookie olmadan tabanlı bir kimlik doğrulama sağlayıcısı ASP.NET Core Identity kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2460d-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2460d-107">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2460d-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2460d-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2460d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2460d-109">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2460d-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2460d-110">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2460d-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2460d-111">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2460d-112">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2460d-113">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2460d-113">Configuration</span></span>

<span data-ttu-id="2460d-114">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetlerini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="2460d-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2460d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2460d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2460d-116">`AuthenticationScheme` birden çok cookie kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2460d-117">`AuthenticationScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, Cookie düzen için bir "s" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="2460d-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2460d-118">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2460d-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2460d-119">Uygulamanın kimlik doğrulama düzeni, uygulamanın cookie kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2460d-120">İçin bir cookie kimlik doğrulama düzeni sağlanmamışsa <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") kullanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2460d-121">Kimlik doğrulamanın cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak olarak ayarlanır `true` .</span><span class="sxs-lookup"><span data-stu-id="2460d-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2460d-122">cookieSite ziyaretçisi veri toplamaya başvurmadıysa kimlik doğrulamaya izin verilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2460d-123">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2460d-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2460d-124">İçinde `Startup.Configure` , `UseAuthentication` `UseAuthorization` özelliği ayarlamak ve `HttpContext.User` Istekler için yetkilendirme ara yazılımını çalıştırmak için öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="2460d-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="2460d-125">`UseAuthentication` `UseAuthorization` Çağrılmadan önce ve yöntemlerini çağırın `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="2460d-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2460d-126"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2460d-127">`CookieAuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2460d-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2460d-128">Cookie İlke ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="2460d-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="2460d-129">[ Cookie Ilke ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , cookie ilke özelliklerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="2460d-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2460d-130">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="2460d-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2460d-131"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie İşlemin genel özelliklerini denetlemek Için Ilke ara yazılım için cookie ve cookie cookie s eklendiği ya da silindiğinde işleme işleyicilerinde kanca için sunulan kullanın.</span><span class="sxs-lookup"><span data-stu-id="2460d-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2460d-132">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2460d-133">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2460d-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2460d-134">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, cookie çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="2460d-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2460d-135">Cookieİçin Ilke ara yazılımı ayarı, `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` aşağıdaki matrisine göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2460d-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2460d-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2460d-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2460d-137">Cookie.SameSite</span></span> | <span data-ttu-id="2460d-138">Sonuç Cookie . SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="2460d-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2460d-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-139">SameSiteMode.None</span></span>     | <span data-ttu-id="2460d-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-140">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-143">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2460d-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2460d-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-147">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2460d-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2460d-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-154">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="2460d-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="2460d-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2460d-160">Kimlik doğrulaması oluşturma cookie</span><span class="sxs-lookup"><span data-stu-id="2460d-160">Create an authentication cookie</span></span>

<span data-ttu-id="2460d-161">cookieTutan Kullanıcı bilgilerini oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2460d-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2460d-162">Kullanıcı bilgileri serileştirilir ve içinde depolanır cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2460d-163"><xref:System.Security.Claims.ClaimsIdentity>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="2460d-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="2460d-164">`SignInAsync` şifreli bir oluşturur cookie ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="2460d-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2460d-165">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2460d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> , varsayılan olarak yalnızca birkaç belirli yolda kullanılır. Örneğin, oturum açma yolu ve oturum kapatma yolları.</span><span class="sxs-lookup"><span data-stu-id="2460d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="2460d-167">Daha fazla bilgi için bkz. [ Cookie authenticationhandler kaynağı](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="2460d-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="2460d-168">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2460d-169">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2460d-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2460d-170">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="2460d-170">Sign out</span></span>

<span data-ttu-id="2460d-171">Geçerli kullanıcının oturumunu kapatmak ve öğesini silmek için cookie şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="2460d-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2460d-172">`CookieAuthenticationDefaults.AuthenticationScheme`(Veya " Cookie s") düzen olarak kullanılmazsa (örneğin, "contoso Cookie "), kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2460d-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2460d-173">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="2460d-174">Tarayıcı kapandığında oturum tabanlı öğeleri otomatik olarak siler cookie (kalıcı olmayan cookie ), ancak cookie tek bir sekme kapalıyken hiçbir s temizlenir.</span><span class="sxs-lookup"><span data-stu-id="2460d-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="2460d-175">Sunucu, sekme veya tarayıcı kapatma olayları hakkında bilgi vermez.</span><span class="sxs-lookup"><span data-stu-id="2460d-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2460d-176">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="2460d-176">React to back-end changes</span></span>

<span data-ttu-id="2460d-177">Bir cookie oluşturulduğunda, cookie tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2460d-178">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="2460d-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2460d-179">Uygulamanın cookie kimlik doğrulama sistemi, kimlik doğrulamasına göre istekleri işlemeye devam eder cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2460d-180">Kimlik doğrulaması geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2460d-181"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Olay, kimliğin doğrulanmasını ve geçerliliğini geçersiz kılmak için kullanılabilir cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2460d-182">cookieHer isteği doğrulamak, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="2460d-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2460d-183">Doğrulamaya yönelik bir yaklaşım cookie , kullanıcı veritabanının değiştiği zaman izlemenin izlenmeye dayanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2460d-184">Veritabanı, Kullanıcı verildikten sonra değiştirilmediyse cookie , hala geçerliyse kullanıcının kimliğinin yeniden doğrulanması gerekmez cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2460d-185">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="2460d-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2460d-186">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2460d-187">cookieVeritabanı değerine göre değiştiğinde bir öğesini geçersiz kılmak için, `LastChanged` cookie `LastChanged` veritabanından geçerli değeri içeren bir talep ile oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="2460d-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="2460d-188">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2460d-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2460d-189">Aşağıda örnek bir uygulama verilmiştir `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2460d-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="2460d-190">Yöntemine hizmet kaydı sırasında olay örneğini kaydedin cookie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2460d-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2460d-191">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `CustomCookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2460d-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2460d-192">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="2460d-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2460d-193">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="2460d-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2460d-194">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="2460d-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2460d-195">cookieHer istekteki tüm kullanıcılar için kimlik doğrulamanın doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2460d-196">Kalıcı cookie s</span><span class="sxs-lookup"><span data-stu-id="2460d-196">Persistent cookies</span></span>

<span data-ttu-id="2460d-197">cookieTarayıcının oturumlarını sürekli olarak sürdürmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2460d-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2460d-198">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2460d-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2460d-199">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve cookie tarayıcı kapanışları aracılığıyla ilerlikli karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2460d-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2460d-200">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2460d-201">cookieTarayıcı kapalıyken zaman aşımı süresi dolarsa, tarayıcı cookie yeniden başlatıldığında onu temizler.</span><span class="sxs-lookup"><span data-stu-id="2460d-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2460d-202">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2460d-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2460d-203">Mutlak cookie süre sonu</span><span class="sxs-lookup"><span data-stu-id="2460d-203">Absolute cookie expiration</span></span>

<span data-ttu-id="2460d-204">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2460d-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2460d-205">Kalıcı oluşturmak için cookie `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2460d-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2460d-206">Aksi takdirde, cookie oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2460d-207">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="2460d-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2460d-208">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve cookie 20 dakika boyunca karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2460d-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2460d-209">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="2460d-209">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="2460d-210">ASP.NET Core Identity , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2460d-211">Ancak, cookie olmadan tabanlı bir kimlik doğrulama sağlayıcısı ASP.NET Core Identity kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2460d-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2460d-212">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2460d-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2460d-213">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2460d-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2460d-214">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2460d-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2460d-215">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2460d-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2460d-216">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2460d-217">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2460d-218">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2460d-218">Configuration</span></span>

<span data-ttu-id="2460d-219">Uygulama [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication için proje dosyasında bir paket başvurusu oluşturun. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) paketi.</span><span class="sxs-lookup"><span data-stu-id="2460d-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="2460d-220">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="2460d-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2460d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2460d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2460d-222">`AuthenticationScheme` birden çok cookie kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2460d-223">`AuthenticationScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, Cookie düzen için bir "s" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="2460d-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2460d-224">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2460d-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2460d-225">Uygulamanın kimlik doğrulama düzeni, uygulamanın cookie kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2460d-226">İçin bir cookie kimlik doğrulama düzeni sağlanmamışsa <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") kullanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2460d-227">Kimlik doğrulamanın cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> özelliği varsayılan olarak olarak ayarlanır `true` .</span><span class="sxs-lookup"><span data-stu-id="2460d-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2460d-228">cookieSite ziyaretçisi veri toplamaya başvurmadıysa kimlik doğrulamaya izin verilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2460d-229">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2460d-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2460d-230">`Startup.Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="2460d-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="2460d-231">`UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="2460d-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2460d-232"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2460d-233">`CookieAuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2460d-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2460d-234">Cookie İlke ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="2460d-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="2460d-235">[ Cookie Ilke ara yazılımı](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) , cookie ilke özelliklerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="2460d-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2460d-236">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="2460d-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2460d-237"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Cookie İşlemin genel özelliklerini denetlemek Için Ilke ara yazılım için cookie ve cookie cookie s eklendiği ya da silindiğinde işleme işleyicilerinde kanca için sunulan kullanın.</span><span class="sxs-lookup"><span data-stu-id="2460d-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2460d-238">Varsayılan <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2460d-239">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2460d-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2460d-240">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, cookie çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="2460d-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2460d-241">Cookieİçin Ilke ara yazılımı ayarı, `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` aşağıdaki matrisine göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2460d-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2460d-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2460d-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2460d-243">Cookie.SameSite</span></span> | <span data-ttu-id="2460d-244">Sonuç Cookie . SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="2460d-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2460d-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-245">SameSiteMode.None</span></span>     | <span data-ttu-id="2460d-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-246">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-249">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2460d-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2460d-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-253">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2460d-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2460d-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2460d-260">SameSiteMode.None</span></span><br><span data-ttu-id="2460d-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2460d-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="2460d-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="2460d-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="2460d-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="2460d-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2460d-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2460d-266">Kimlik doğrulaması oluşturma cookie</span><span class="sxs-lookup"><span data-stu-id="2460d-266">Create an authentication cookie</span></span>

<span data-ttu-id="2460d-267">cookieTutan Kullanıcı bilgilerini oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2460d-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2460d-268">Kullanıcı bilgileri serileştirilir ve içinde depolanır cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2460d-269"><xref:System.Security.Claims.ClaimsIdentity>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="2460d-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="2460d-270">`SignInAsync` şifreli bir oluşturur cookie ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="2460d-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2460d-271">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2460d-272">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2460d-273">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2460d-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2460d-274">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="2460d-274">Sign out</span></span>

<span data-ttu-id="2460d-275">Geçerli kullanıcının oturumunu kapatmak ve öğesini silmek için cookie şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="2460d-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2460d-276">`CookieAuthenticationDefaults.AuthenticationScheme`(Veya " Cookie s") düzen olarak kullanılmazsa (örneğin, "contoso Cookie "), kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2460d-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2460d-277">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2460d-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2460d-278">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="2460d-278">React to back-end changes</span></span>

<span data-ttu-id="2460d-279">Bir cookie oluşturulduğunda, cookie tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="2460d-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2460d-280">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="2460d-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2460d-281">Uygulamanın cookie kimlik doğrulama sistemi, kimlik doğrulamasına göre istekleri işlemeye devam eder cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2460d-282">Kimlik doğrulaması geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2460d-283"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Olay, kimliğin doğrulanmasını ve geçerliliğini geçersiz kılmak için kullanılabilir cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2460d-284">cookieHer isteği doğrulamak, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="2460d-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2460d-285">Doğrulamaya yönelik bir yaklaşım cookie , kullanıcı veritabanının değiştiği zaman izlemenin izlenmeye dayanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2460d-286">Veritabanı, Kullanıcı verildikten sonra değiştirilmediyse cookie , hala geçerliyse kullanıcının kimliğinin yeniden doğrulanması gerekmez cookie .</span><span class="sxs-lookup"><span data-stu-id="2460d-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2460d-287">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="2460d-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2460d-288">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2460d-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2460d-289">cookieVeritabanı değerine göre değiştiğinde bir öğesini geçersiz kılmak için, `LastChanged` cookie `LastChanged` veritabanından geçerli değeri içeren bir talep ile oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="2460d-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="2460d-290">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2460d-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2460d-291">Aşağıda örnek bir uygulama verilmiştir `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2460d-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="2460d-292">Yöntemine hizmet kaydı sırasında olay örneğini kaydedin cookie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2460d-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2460d-293">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `CustomCookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2460d-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2460d-294">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="2460d-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2460d-295">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="2460d-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2460d-296">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="2460d-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2460d-297">cookieHer istekteki tüm kullanıcılar için kimlik doğrulamanın doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2460d-298">Kalıcı cookie s</span><span class="sxs-lookup"><span data-stu-id="2460d-298">Persistent cookies</span></span>

<span data-ttu-id="2460d-299">cookieTarayıcının oturumlarını sürekli olarak sürdürmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2460d-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2460d-300">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2460d-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2460d-301">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve cookie tarayıcı kapanışları aracılığıyla ilerlikli karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2460d-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2460d-302">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2460d-303">cookieTarayıcı kapalıyken zaman aşımı süresi dolarsa, tarayıcı cookie yeniden başlatıldığında onu temizler.</span><span class="sxs-lookup"><span data-stu-id="2460d-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2460d-304">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2460d-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2460d-305">Mutlak cookie süre sonu</span><span class="sxs-lookup"><span data-stu-id="2460d-305">Absolute cookie expiration</span></span>

<span data-ttu-id="2460d-306">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2460d-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2460d-307">Kalıcı oluşturmak için cookie `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2460d-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2460d-308">Aksi takdirde, cookie oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="2460d-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2460d-309">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="2460d-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2460d-310">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve cookie 20 dakika boyunca karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2460d-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2460d-311">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="2460d-311">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2460d-312">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2460d-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="2460d-313">İlke tabanlı rol denetimleri</span><span class="sxs-lookup"><span data-stu-id="2460d-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
