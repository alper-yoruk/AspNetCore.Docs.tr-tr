---
title: ':::no-loc(cookie):::Kimlik doğrulamasını kullanmadan kullanın:::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: ':::no-loc(cookie):::Kimlik doğrulamasını olmadan nasıl kullanacağınızı öğrenin :::no-loc(ASP.NET Core Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061371"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="e9eb3-103">:::no-loc(cookie):::Kimlik doğrulamasını kullanmadan kullanın:::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="e9eb3-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="e9eb3-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e9eb3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9eb3-105">:::no-loc(ASP.NET Core Identity)::: , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e9eb3-106">Ancak, :::no-loc(cookie)::: olmadan tabanlı bir kimlik doğrulama sağlayıcısı :::no-loc(ASP.NET Core Identity)::: kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="e9eb3-107">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e9eb3-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9eb3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e9eb3-109">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e9eb3-110">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e9eb3-111">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e9eb3-112">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e9eb3-113">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e9eb3-113">Configuration</span></span>

<span data-ttu-id="e9eb3-114">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetlerini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e9eb3-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e9eb3-116">`AuthenticationScheme` birden çok :::no-loc(cookie)::: kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e9eb3-117">`AuthenticationScheme` [ :::no-loc(Cookie)::: Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, :::no-loc(Cookie)::: düzen için bir "s" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="e9eb3-118">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e9eb3-119">Uygulamanın kimlik doğrulama düzeni, uygulamanın :::no-loc(cookie)::: kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="e9eb3-120">İçin bir :::no-loc(cookie)::: kimlik doğrulama düzeni sağlanmamışsa <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="e9eb3-121">Kimlik doğrulamanın :::no-loc(cookie)::: <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> özelliği varsayılan olarak olarak ayarlanır `true` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e9eb3-122">:::no-loc(cookie):::Site ziyaretçisi veri toplamaya başvurmadıysa kimlik doğrulamaya izin verilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e9eb3-123">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="e9eb3-124">İçinde `Startup.Configure` , `UseAuthentication` `UseAuthorization` özelliği ayarlamak ve `HttpContext.User` Istekler için yetkilendirme ara yazılımını çalıştırmak için öğesini çağırın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e9eb3-125">`UseAuthentication` `UseAuthorization` Çağrılmadan önce ve yöntemlerini çağırın `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e9eb3-126"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e9eb3-127">`:::no-loc(Cookie):::AuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e9eb3-128">:::no-loc(Cookie)::: İlke ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="e9eb3-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="e9eb3-129">[ :::no-loc(Cookie)::: Ilke ara yazılımı](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) , :::no-loc(cookie)::: ilke özelliklerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="e9eb3-130">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="e9eb3-131"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> :::no-loc(Cookie)::: İşlemin genel özelliklerini denetlemek Için Ilke ara yazılım için :::no-loc(cookie)::: ve :::no-loc(cookie)::: :::no-loc(cookie)::: s eklendiği ya da silindiğinde işleme işleyicilerinde kanca için sunulan kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="e9eb3-132">Varsayılan <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e9eb3-133">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e9eb3-134">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, :::no-loc(cookie)::: çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e9eb3-135">:::no-loc(Cookie):::İçin Ilke ara yazılımı ayarı, `MinimumSameSitePolicy` `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` aşağıdaki matrisine göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e9eb3-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e9eb3-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e9eb3-137">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="e9eb3-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="e9eb3-138">Sonuç :::no-loc(Cookie)::: . SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="e9eb3-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e9eb3-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-139">SameSiteMode.None</span></span>     | <span data-ttu-id="e9eb3-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-140">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-143">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e9eb3-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e9eb3-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-147">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e9eb3-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e9eb3-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-154">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="e9eb3-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e9eb3-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e9eb3-160">Kimlik doğrulaması oluşturma :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e9eb3-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="e9eb3-161">:::no-loc(cookie):::Tutan Kullanıcı bilgilerini oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e9eb3-162">Kullanıcı bilgileri serileştirilir ve içinde depolanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="e9eb3-163"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="e9eb3-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e9eb3-164">`SignInAsync` şifreli bir oluşturur :::no-loc(cookie)::: ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="e9eb3-165">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e9eb3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> , varsayılan olarak yalnızca birkaç belirli yolda kullanılır. Örneğin, oturum açma yolu ve oturum kapatma yolları.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e9eb3-167">Daha fazla bilgi için bkz. [ :::no-loc(Cookie)::: authenticationhandler kaynağı](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="e9eb3-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e9eb3-168">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e9eb3-169">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e9eb3-170">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="e9eb3-170">Sign out</span></span>

<span data-ttu-id="e9eb3-171">Geçerli kullanıcının oturumunu kapatmak ve öğesini silmek için :::no-loc(cookie)::: şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e9eb3-172">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(Veya " :::no-loc(Cookie)::: s") düzen olarak kullanılmazsa (örneğin, "contoso :::no-loc(Cookie)::: "), kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e9eb3-173">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="e9eb3-174">Tarayıcı kapandığında oturum tabanlı öğeleri otomatik olarak siler :::no-loc(cookie)::: (kalıcı olmayan :::no-loc(cookie)::: ), ancak :::no-loc(cookie)::: tek bir sekme kapalıyken hiçbir s temizlenir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="e9eb3-175">Sunucu, sekme veya tarayıcı kapatma olayları hakkında bilgi vermez.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e9eb3-176">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="e9eb3-176">React to back-end changes</span></span>

<span data-ttu-id="e9eb3-177">Bir :::no-loc(cookie)::: oluşturulduğunda, :::no-loc(cookie)::: tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="e9eb3-178">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="e9eb3-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e9eb3-179">Uygulamanın :::no-loc(cookie)::: kimlik doğrulama sistemi, kimlik doğrulamasına göre istekleri işlemeye devam eder :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="e9eb3-180">Kimlik doğrulaması geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="e9eb3-181"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>Olay, kimliğin doğrulanmasını ve geçerliliğini geçersiz kılmak için kullanılabilir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="e9eb3-182">:::no-loc(cookie):::Her isteği doğrulamak, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e9eb3-183">Doğrulamaya yönelik bir yaklaşım :::no-loc(cookie)::: , kullanıcı veritabanının değiştiği zaman izlemenin izlenmeye dayanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e9eb3-184">Veritabanı, Kullanıcı verildikten sonra değiştirilmediyse :::no-loc(cookie)::: , hala geçerliyse kullanıcının kimliğinin yeniden doğrulanması gerekmez :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="e9eb3-185">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e9eb3-186">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e9eb3-187">:::no-loc(cookie):::Veritabanı değerine göre değiştiğinde bir öğesini geçersiz kılmak için, `LastChanged` :::no-loc(cookie)::: `LastChanged` veritabanından geçerli değeri içeren bir talep ile oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="e9eb3-188">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="e9eb3-189">Aşağıda örnek bir uygulama verilmiştir `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="e9eb3-190">Yöntemine hizmet kaydı sırasında olay örneğini kaydedin :::no-loc(cookie)::: `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e9eb3-191">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `Custom:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="e9eb3-192">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e9eb3-193">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e9eb3-194">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e9eb3-195">:::no-loc(cookie):::Her istekteki tüm kullanıcılar için kimlik doğrulamanın doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e9eb3-196">Kalıcı :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="e9eb3-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="e9eb3-197">:::no-loc(cookie):::Tarayıcının oturumlarını sürekli olarak sürdürmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="e9eb3-198">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e9eb3-199">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve :::no-loc(cookie)::: tarayıcı kapanışları aracılığıyla ilerlikli karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="e9eb3-200">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e9eb3-201">:::no-loc(cookie):::Tarayıcı kapalıyken zaman aşımı süresi dolarsa, tarayıcı :::no-loc(cookie)::: yeniden başlatıldığında onu temizler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="e9eb3-202">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e9eb3-203">Mutlak :::no-loc(cookie)::: süre sonu</span><span class="sxs-lookup"><span data-stu-id="e9eb3-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="e9eb3-204">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e9eb3-205">Kalıcı oluşturmak için :::no-loc(cookie)::: `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e9eb3-206">Aksi takdirde, :::no-loc(cookie)::: oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e9eb3-207">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e9eb3-208">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve :::no-loc(cookie)::: 20 dakika boyunca karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="e9eb3-209">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9eb3-210">:::no-loc(ASP.NET Core Identity)::: , oturum açma işlemleri oluşturmaya ve korumaya yönelik eksiksiz, tam özellikli bir kimlik doğrulama sağlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e9eb3-211">Ancak, :::no-loc(cookie)::: olmadan tabanlı bir kimlik doğrulama sağlayıcısı :::no-loc(ASP.NET Core Identity)::: kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="e9eb3-212">Daha fazla bilgi için bkz. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e9eb3-213">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9eb3-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e9eb3-214">Örnek uygulamadaki tanıtım amacıyla, Maria Rodriguez olan kuramsal kullanıcının Kullanıcı hesabı, uygulamaya sabit olarak kodlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e9eb3-215">Kullanıcı oturumu açmak için **e-posta** adresini `maria.rodriguez@contoso.com` ve parolayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e9eb3-216">Kullanıcının kimliği, `AuthenticateUser` *Sayfalar/Account/Login. cshtml. cs* dosyasındaki yönteminde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e9eb3-217">Gerçek dünyada bir örnekte, kullanıcının kimliği bir veritabanında doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e9eb3-218">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e9eb3-218">Configuration</span></span>

<span data-ttu-id="e9eb3-219">Uygulama [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)kullanmıyorsa, [Microsoft. Aspnetcore. Authentication için proje dosyasında bir paket başvurusu oluşturun. :::no-loc(Cookie)::: s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) paketi.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="e9eb3-220">Yönteminde, `Startup.ConfigureServices` ve yöntemleriyle kimlik doğrulama ara yazılım hizmetini oluşturun <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e9eb3-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> geçildi `AddAuthentication` , uygulamanın varsayılan kimlik doğrulama şemasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e9eb3-222">`AuthenticationScheme` birden çok :::no-loc(cookie)::: kimlik doğrulaması örneği olduğunda ve [belirli bir şemayla yetkilendirmek](xref:security/authorization/limitingidentitybyscheme)istediğinizde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e9eb3-223">`AuthenticationScheme` [ :::no-loc(Cookie)::: Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) olarak ayarlanması, :::no-loc(Cookie)::: düzen için bir "s" değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="e9eb3-224">Düzeni ayıran herhangi bir dize değeri sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e9eb3-225">Uygulamanın kimlik doğrulama düzeni, uygulamanın :::no-loc(cookie)::: kimlik doğrulama düzeninden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="e9eb3-226">İçin bir :::no-loc(cookie)::: kimlik doğrulama düzeni sağlanmamışsa <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="e9eb3-227">Kimlik doğrulamanın :::no-loc(cookie)::: <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> özelliği varsayılan olarak olarak ayarlanır `true` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e9eb3-228">:::no-loc(cookie):::Site ziyaretçisi veri toplamaya başvurmadıysa kimlik doğrulamaya izin verilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e9eb3-229">Daha fazla bilgi için bkz. <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="e9eb3-230">`Startup.Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e9eb3-231">`UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e9eb3-232"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>Sınıfı, kimlik doğrulama sağlayıcısı seçeneklerini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e9eb3-233">`:::no-loc(Cookie):::AuthenticationOptions`Yönteminde kimlik doğrulaması için hizmet yapılandırmasında ayarlanır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e9eb3-234">:::no-loc(Cookie)::: İlke ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="e9eb3-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="e9eb3-235">[ :::no-loc(Cookie)::: Ilke ara yazılımı](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) , :::no-loc(cookie)::: ilke özelliklerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="e9eb3-236">Ara yazılımı uygulama işleme işlem hattına eklemek, &mdash; yalnızca işlem hattına kaydedilen aşağı akış bileşenlerini etkiler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="e9eb3-237"><xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> :::no-loc(Cookie)::: İşlemin genel özelliklerini denetlemek Için Ilke ara yazılım için :::no-loc(cookie)::: ve :::no-loc(cookie)::: :::no-loc(cookie)::: s eklendiği ya da silindiğinde işleme işleyicilerinde kanca için sunulan kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="e9eb3-238">Varsayılan <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> değer `SameSiteMode.Lax` OAuth2 kimlik doğrulamasına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e9eb3-239">Aynı site ilkesini kesinlikle zorlamak için, `SameSiteMode.Strict` öğesini ayarlayın `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e9eb3-240">Bu ayar, OAuth2 ve diğer çapraz kaynak kimlik doğrulama düzenlerini kesse de, :::no-loc(cookie)::: çıkış noktaları arası istek işlemeye bağlı olmayan diğer uygulama türleri için güvenlik düzeyini yükseltir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e9eb3-241">:::no-loc(Cookie):::İçin Ilke ara yazılımı ayarı, `MinimumSameSitePolicy` `:::no-loc(Cookie):::.SameSite` `:::no-loc(Cookie):::AuthenticationOptions` aşağıdaki matrisine göre ayarlar ' ın ayarını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e9eb3-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e9eb3-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e9eb3-243">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="e9eb3-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="e9eb3-244">Sonuç :::no-loc(Cookie)::: . SameSite ayarı</span><span class="sxs-lookup"><span data-stu-id="e9eb3-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e9eb3-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-245">SameSiteMode.None</span></span>     | <span data-ttu-id="e9eb3-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-246">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-249">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e9eb3-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e9eb3-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-253">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e9eb3-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e9eb3-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="e9eb3-260">SameSiteMode.None</span></span><br><span data-ttu-id="e9eb3-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="e9eb3-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="e9eb3-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="e9eb3-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="e9eb3-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="e9eb3-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="e9eb3-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e9eb3-266">Kimlik doğrulaması oluşturma :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="e9eb3-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="e9eb3-267">:::no-loc(cookie):::Tutan Kullanıcı bilgilerini oluşturmak için, oluşturun <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e9eb3-268">Kullanıcı bilgileri serileştirilir ve içinde depolanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="e9eb3-269"><xref:System.Security.Claims.Claims:::no-loc(Identity):::>Gerekli tüm öğeleri içeren bir oluşturun <xref:System.Security.Claims.Claim> ve <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Kullanıcı oturumunu açmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="e9eb3-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e9eb3-270">`SignInAsync` şifreli bir oluşturur :::no-loc(cookie)::: ve geçerli yanıta ekler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="e9eb3-271">`AuthenticationScheme`Belirtilmemişse, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e9eb3-272">ASP.NET Core [veri koruma](xref:security/data-protection/using-data-protection) sistemi şifreleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e9eb3-273">Birden çok makinede barındırılan bir uygulama, uygulamalar arasında yük dengeleme veya bir Web grubu kullanma için, [veri korumayı](xref:security/data-protection/configuration/overview) aynı anahtar halkasını ve uygulama tanımlayıcısını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e9eb3-274">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="e9eb3-274">Sign out</span></span>

<span data-ttu-id="e9eb3-275">Geçerli kullanıcının oturumunu kapatmak ve öğesini silmek için :::no-loc(cookie)::: şunu arayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e9eb3-276">`:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`(Veya " :::no-loc(Cookie)::: s") düzen olarak kullanılmazsa (örneğin, "contoso :::no-loc(Cookie)::: "), kimlik doğrulama sağlayıcısını yapılandırırken kullanılan düzeni sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e9eb3-277">Aksi takdirde, varsayılan düzen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e9eb3-278">Arka uç değişikliklerine tepki verme</span><span class="sxs-lookup"><span data-stu-id="e9eb3-278">React to back-end changes</span></span>

<span data-ttu-id="e9eb3-279">Bir :::no-loc(cookie)::: oluşturulduğunda, :::no-loc(cookie)::: tek kimlik kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="e9eb3-280">Arka uç sistemlerinde bir kullanıcı hesabı devre dışı bırakılmışsa:</span><span class="sxs-lookup"><span data-stu-id="e9eb3-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e9eb3-281">Uygulamanın :::no-loc(cookie)::: kimlik doğrulama sistemi, kimlik doğrulamasına göre istekleri işlemeye devam eder :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="e9eb3-282">Kimlik doğrulaması geçerli olduğu sürece kullanıcı uygulamada oturum açmış durumda kalır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="e9eb3-283"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*>Olay, kimliğin doğrulanmasını ve geçerliliğini geçersiz kılmak için kullanılabilir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="e9eb3-284">:::no-loc(cookie):::Her isteği doğrulamak, uygulamaya erişen kullanıcıların iptal edilmesinin riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e9eb3-285">Doğrulamaya yönelik bir yaklaşım :::no-loc(cookie)::: , kullanıcı veritabanının değiştiği zaman izlemenin izlenmeye dayanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e9eb3-286">Veritabanı, Kullanıcı verildikten sonra değiştirilmediyse :::no-loc(cookie)::: , hala geçerliyse kullanıcının kimliğinin yeniden doğrulanması gerekmez :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="e9eb3-287">Örnek uygulamada, veritabanı ' de uygulanır `IUserRepository` ve bir `LastChanged` değer depolar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e9eb3-288">Veritabanında bir Kullanıcı güncellenmiştir, `LastChanged` değer geçerli saate ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e9eb3-289">:::no-loc(cookie):::Veritabanı değerine göre değiştiğinde bir öğesini geçersiz kılmak için, `LastChanged` :::no-loc(cookie)::: `LastChanged` veritabanından geçerli değeri içeren bir talep ile oluşturun `LastChanged` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="e9eb3-290">Olay için bir geçersiz kılma uygulamak üzere `ValidatePrincipal` , aşağıdakilerden türetilen bir sınıfa aşağıdaki imzaya sahip bir yöntem yazın <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="e9eb3-291">Aşağıda örnek bir uygulama verilmiştir `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="e9eb3-292">Yöntemine hizmet kaydı sırasında olay örneğini kaydedin :::no-loc(cookie)::: `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e9eb3-293">Sınıfınız için [kapsamlı bir hizmet kaydı](xref:fundamentals/dependency-injection#service-lifetimes) sağlayın `Custom:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="e9eb3-294">Kullanıcı adının, &mdash; güvenliği hiçbir şekilde etkilemeyen bir kararı güncelleştirmediği bir durum düşünün.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e9eb3-295">Kullanıcı sorumlusunu kalıcı olarak güncelleştirmek istiyorsanız, çağırın `context.ReplacePrincipal` ve `context.ShouldRenew` özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e9eb3-296">Burada açıklanan yaklaşım her istekte tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e9eb3-297">:::no-loc(cookie):::Her istekteki tüm kullanıcılar için kimlik doğrulamanın doğrulanması, uygulama için büyük bir performans cezası oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e9eb3-298">Kalıcı :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="e9eb3-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="e9eb3-299">:::no-loc(cookie):::Tarayıcının oturumlarını sürekli olarak sürdürmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="e9eb3-300">Bu Kalıcılık yalnızca oturum açma veya benzer bir mekanizmanın "Beni anımsa" onay kutusu ile açık kullanıcı onayı ile etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e9eb3-301">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve :::no-loc(cookie)::: tarayıcı kapanışları aracılığıyla ilerlikli karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="e9eb3-302">Daha önce yapılandırılmış tüm Kayan süre sonu ayarları kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e9eb3-303">:::no-loc(cookie):::Tarayıcı kapalıyken zaman aşımı süresi dolarsa, tarayıcı :::no-loc(cookie)::: yeniden başlatıldığında onu temizler.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="e9eb3-304">Şu <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> şekilde `true` ayarlayın <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e9eb3-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e9eb3-305">Mutlak :::no-loc(cookie)::: süre sonu</span><span class="sxs-lookup"><span data-stu-id="e9eb3-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="e9eb3-306">Mutlak bir sona erme saati ile ayarlanabilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e9eb3-307">Kalıcı oluşturmak için :::no-loc(cookie)::: `IsPersistent` de ayarlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e9eb3-308">Aksi takdirde, :::no-loc(cookie)::: oturum tabanlı bir yaşam süresi ile oluşturulur ve bu kimlik doğrulama biletinden önce ya da sonra zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e9eb3-309">, Ayarlandığında, `ExpiresUtc` seçeneğinin değerini geçersiz kılar <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="e9eb3-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e9eb3-310">Aşağıdaki kod parçacığı, bir kimlik oluşturur ve :::no-loc(cookie)::: 20 dakika boyunca karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="e9eb3-311">Bu, daha önce yapılandırılmış olan tüm Kayan süre sonu ayarlarını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="e9eb3-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e9eb3-312">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9eb3-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e9eb3-313">İlke tabanlı rol denetimleri</span><span class="sxs-lookup"><span data-stu-id="e9eb3-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
