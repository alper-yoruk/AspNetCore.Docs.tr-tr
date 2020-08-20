---
title: No olmadan Facebook, Google ve dış sağlayıcı kimlik doğrulaması ASP.NET Core Identity
author: rick-anderson
description: Facebook, Google, Twitter vb. hesap Kullanıcı kimlik doğrulamasını kullanma açıklaması ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: a91a2f2fb7873e5a672c624e9cf863ae720c8005
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634234"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="a18bd-103">Şu olmadan sosyal oturum açma sağlayıcısı kimlik doğrulamasını kullanma ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="a18bd-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="a18bd-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a18bd-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a18bd-105"><xref:security/authentication/social/index> Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="a18bd-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="a18bd-106">Bu konuda açıklanan yaklaşım, ASP.NET Core Identity kimlik doğrulama sağlayıcısı olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="a18bd-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="a18bd-107">Bu örnek, **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a18bd-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="a18bd-108">Bu, tüm özelliklerini gerektirmeyen ASP.NET Core Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısı ile tümleştirme gerektiren uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="a18bd-109">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="a18bd-110">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="a18bd-111">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="a18bd-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="a18bd-112">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a18bd-113">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a18bd-114">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a18bd-115">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="a18bd-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="a18bd-116">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a18bd-116">Configuration</span></span>

<span data-ttu-id="a18bd-117">Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="a18bd-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="a18bd-118">Uygulamanın ' i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ayarlayan çağrı <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="a18bd-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="a18bd-119">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="a18bd-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="a18bd-120">Uygulamanın `DefaultScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") olarak ayarlanması, uygulamayı Cookie Bu uzantı yöntemlerinin varsayılan şeması olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="a18bd-121">Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="a18bd-122">`DefaultChallengeScheme` geçersiz kılmalar `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="a18bd-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..</span><span class="sxs-lookup"><span data-stu-id="a18bd-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="a18bd-124">' De `Startup.Configure` , `UseAuthentication` `UseAuthorization` çağırma ve arasında çağrı yapın `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="a18bd-125">Bu, `HttpContext.User` özelliği ayarlar ve istekler Için yetkilendirme ara yazılımını çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="a18bd-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="a18bd-126">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="a18bd-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="a18bd-127">Kimlik doğrulaması hakkında daha fazla bilgi için cookie bkz <xref:security/authentication/cookie> ..</span><span class="sxs-lookup"><span data-stu-id="a18bd-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="a18bd-128">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="a18bd-128">Apply authorization</span></span>

<span data-ttu-id="a18bd-129">`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="a18bd-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="a18bd-130">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="a18bd-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="a18bd-131">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="a18bd-131">Sign out</span></span>

<span data-ttu-id="a18bd-132">Geçerli kullanıcının oturumunu kapatmak ve bunları silmek için cookie [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="a18bd-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="a18bd-133">Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="a18bd-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="a18bd-134">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="a18bd-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="a18bd-135">Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a18bd-136">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a18bd-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a18bd-137"><xref:security/authentication/social/index> Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="a18bd-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="a18bd-138">Bu konuda açıklanan yaklaşım, ASP.NET Core Identity kimlik doğrulama sağlayıcısı olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="a18bd-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="a18bd-139">Bu örnek, **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a18bd-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="a18bd-140">Bu, tüm özelliklerini gerektirmeyen ASP.NET Core Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısı ile tümleştirme gerektiren uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="a18bd-141">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="a18bd-142">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="a18bd-143">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="a18bd-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="a18bd-144">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a18bd-145">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a18bd-146">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a18bd-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a18bd-147">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="a18bd-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="a18bd-148">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a18bd-148">Configuration</span></span>

<span data-ttu-id="a18bd-149">Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın `AddAuthentication` `AddCookie` `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="a18bd-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="a18bd-150">[Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) çağrısı, uygulamanın [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a18bd-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="a18bd-151">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="a18bd-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="a18bd-152">Uygulamanın `DefaultScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") olarak ayarlanması, uygulamayı Cookie Bu uzantı yöntemlerinin varsayılan şeması olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="a18bd-153">Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="a18bd-154">`DefaultChallengeScheme` geçersiz kılmalar `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="a18bd-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..</span><span class="sxs-lookup"><span data-stu-id="a18bd-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="a18bd-156">`Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="a18bd-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="a18bd-157">`UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="a18bd-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="a18bd-158">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="a18bd-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="a18bd-159">Kimlik doğrulaması hakkında daha fazla bilgi için cookie bkz <xref:security/authentication/cookie> ..</span><span class="sxs-lookup"><span data-stu-id="a18bd-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="a18bd-160">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="a18bd-160">Apply authorization</span></span>

<span data-ttu-id="a18bd-161">`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="a18bd-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="a18bd-162">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="a18bd-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="a18bd-163">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="a18bd-163">Sign out</span></span>

<span data-ttu-id="a18bd-164">Geçerli kullanıcının oturumunu kapatmak ve bunları silmek için cookie [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="a18bd-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="a18bd-165">Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="a18bd-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="a18bd-166">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="a18bd-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="a18bd-167">Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a18bd-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a18bd-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a18bd-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
