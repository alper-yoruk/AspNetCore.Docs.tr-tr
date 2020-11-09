---
title: 'No olmadan Facebook, Google ve dış sağlayıcı kimlik doğrulaması ASP.NET Core Identity'
author: rick-anderson
description: 'Facebook, Google, Twitter vb. hesap Kullanıcı kimlik doğrulamasını kullanma açıklaması ASP.NET Core Identity .'
ms.author: riande
ms.date: 12/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060293"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="1a31f-103">Şu olmadan sosyal oturum açma sağlayıcısı kimlik doğrulamasını kullanma ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1a31f-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="1a31f-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1a31f-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a31f-105"><xref:security/authentication/social/index> Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="1a31f-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="1a31f-106">Bu konuda açıklanan yaklaşım, ASP.NET Core Identity kimlik doğrulama sağlayıcısı olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="1a31f-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="1a31f-107">Bu örnek, **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1a31f-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="1a31f-108">Bu, tüm özelliklerini gerektirmeyen ASP.NET Core Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısı ile tümleştirme gerektiren uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="1a31f-109">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="1a31f-110">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="1a31f-111">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="1a31f-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="1a31f-112">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1a31f-113">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1a31f-114">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1a31f-115">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="1a31f-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="1a31f-116">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1a31f-116">Configuration</span></span>

<span data-ttu-id="1a31f-117">Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="1a31f-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="1a31f-118">Uygulamanın ' i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ayarlayan çağrı <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="1a31f-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="1a31f-119">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="1a31f-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="1a31f-120">Uygulamanın `DefaultScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") olarak ayarlanması, uygulamayı Cookie Bu uzantı yöntemlerinin varsayılan şeması olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="1a31f-121">Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="1a31f-122">`DefaultChallengeScheme` geçersiz kılmalar `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="1a31f-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..</span><span class="sxs-lookup"><span data-stu-id="1a31f-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="1a31f-124">' De `Startup.Configure` , `UseAuthentication` `UseAuthorization` çağırma ve arasında çağrı yapın `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="1a31f-125">Bu, `HttpContext.User` özelliği ayarlar ve istekler Için yetkilendirme ara yazılımını çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="1a31f-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="1a31f-126">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="1a31f-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="1a31f-127">Kimlik doğrulaması hakkında daha fazla bilgi için cookie bkz <xref:security/authentication/cookie> ..</span><span class="sxs-lookup"><span data-stu-id="1a31f-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="1a31f-128">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="1a31f-128">Apply authorization</span></span>

<span data-ttu-id="1a31f-129">`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="1a31f-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="1a31f-130">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="1a31f-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="1a31f-131">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="1a31f-131">Sign out</span></span>

<span data-ttu-id="1a31f-132">Geçerli kullanıcının oturumunu kapatmak ve bunları silmek için cookie [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="1a31f-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="1a31f-133">Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="1a31f-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="1a31f-134">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="1a31f-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="1a31f-135">Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a31f-136">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1a31f-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a31f-137"><xref:security/authentication/social/index> Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="1a31f-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="1a31f-138">Bu konuda açıklanan yaklaşım, ASP.NET Core Identity kimlik doğrulama sağlayıcısı olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="1a31f-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="1a31f-139">Bu örnek, **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1a31f-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="1a31f-140">Bu, tüm özelliklerini gerektirmeyen ASP.NET Core Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısı ile tümleştirme gerektiren uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="1a31f-141">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="1a31f-142">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="1a31f-143">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="1a31f-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="1a31f-144">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="1a31f-145">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="1a31f-146">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="1a31f-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="1a31f-147">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="1a31f-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="1a31f-148">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1a31f-148">Configuration</span></span>

<span data-ttu-id="1a31f-149">Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın `AddAuthentication` `AddCookie` `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="1a31f-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="1a31f-150">[Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) çağrısı, uygulamanın [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1a31f-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="1a31f-151">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="1a31f-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="1a31f-152">Uygulamanın `DefaultScheme` [ Cookie Authenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") olarak ayarlanması, uygulamayı Cookie Bu uzantı yöntemlerinin varsayılan şeması olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="1a31f-153">Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="1a31f-154">`DefaultChallengeScheme` geçersiz kılmalar `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="1a31f-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..</span><span class="sxs-lookup"><span data-stu-id="1a31f-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="1a31f-156">`Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="1a31f-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="1a31f-157">`UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="1a31f-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="1a31f-158">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="1a31f-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="1a31f-159">Kimlik doğrulaması hakkında daha fazla bilgi için cookie bkz <xref:security/authentication/cookie> ..</span><span class="sxs-lookup"><span data-stu-id="1a31f-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="1a31f-160">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="1a31f-160">Apply authorization</span></span>

<span data-ttu-id="1a31f-161">`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="1a31f-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="1a31f-162">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="1a31f-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="1a31f-163">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="1a31f-163">Sign out</span></span>

<span data-ttu-id="1a31f-164">Geçerli kullanıcının oturumunu kapatmak ve bunları silmek için cookie [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="1a31f-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="1a31f-165">Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="1a31f-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="1a31f-166">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="1a31f-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="1a31f-167">Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a31f-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a31f-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1a31f-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
