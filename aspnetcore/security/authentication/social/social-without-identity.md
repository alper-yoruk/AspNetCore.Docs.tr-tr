---
title: ASP.NET Core olmadan Facebook, Google ve dış sağlayıcı kimlik doğrulamasıIdentity
author: rick-anderson
description: Facebook, Google, Twitter vb. hesap Kullanıcı kimlik doğrulamasını ASP.NET Core olmadan kullanma açıklaması Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406049"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core olmadan sosyal oturum açma sağlayıcısı kimlik doğrulamasını kullanmaIdentity

, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar. Bu konu başlığı altında açıklanan yaklaşım, Identity kimlik doğrulama sağlayıcısı olarak ASP.NET Core içerir.

Bu örnek, ASP.NET Core **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir Identity . Bu, tüm ASP.NET Core özelliklerinin gerekli olmadığı Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısıyla tümleştirme gerektirdiğinden uygulamalar için yararlıdır.

Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır. Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır. Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:

* [Facebook kimlik doğrulaması](xref:security/authentication/facebook-logins)
* [Microsoft kimlik doğrulaması](xref:security/authentication/microsoft-logins)
* [Twitter kimlik doğrulaması](xref:security/authentication/twitter-logins)
* [Diğer sağlayıcılar](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Yapılandırma

Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Uygulamanın ' i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ayarlayan çağrı <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . , `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Uygulamanın, `DefaultScheme` tanımlama bilgisi olan [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") olarak ayarlanması, uygulamayı bu uzantı yöntemlerinin varsayılan şeması olarak tanımlama bilgilerini kullanacak şekilde yapılandırır. Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` . `DefaultChallengeScheme`geçersiz kılmalar `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..

' De `Startup.Configure` , `UseAuthentication` `UseAuthorization` çağırma ve arasında çağrı yapın `UseRouting` `UseEndpoints` . Bu, `HttpContext.User` özelliği ayarlar ve istekler Için yetkilendirme ara yazılımını çalıştırır:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts). Tanımlama bilgisi kimlik doğrulaması hakkında daha fazla bilgi için bkz <xref:security/authentication/cookie> ..

## <a name="apply-authorization"></a>Yetkilendirmeyi uygula

`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin. Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Oturumu kapat

Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağırın. Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin. Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar. Bu konu başlığı altında açıklanan yaklaşım, Identity kimlik doğrulama sağlayıcısı olarak ASP.NET Core içerir.

Bu örnek, ASP.NET Core **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir Identity . Bu, tüm ASP.NET Core özelliklerinin gerekli olmadığı Identity , ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısıyla tümleştirme gerektirdiğinden uygulamalar için yararlıdır.

Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır. Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır. Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:

* [Facebook kimlik doğrulaması](xref:security/authentication/facebook-logins)
* [Microsoft kimlik doğrulaması](xref:security/authentication/microsoft-logins)
* [Twitter kimlik doğrulaması](xref:security/authentication/twitter-logins)
* [Diğer sağlayıcılar](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Yapılandırma

Yönteminde,, `ConfigureServices` ve yöntemleriyle uygulamanın kimlik doğrulama düzenlerini yapılandırın `AddAuthentication` `AddCookie` `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

[Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) çağrısı, uygulamanın [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)değerini ayarlar. , `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Uygulamanın, `DefaultScheme` tanımlama bilgisi olan [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") olarak ayarlanması, uygulamayı bu uzantı yöntemlerinin varsayılan şeması olarak tanımlama bilgilerini kullanacak şekilde yapılandırır. Uygulamanın <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır `ChallengeAsync` . `DefaultChallengeScheme`geçersiz kılmalar `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Ayarlandığında geçersiz kılan ek özellikler için bkz `DefaultScheme` ..

`Configure`Yönteminde, `UseAuthentication` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için yöntemini çağırın `HttpContext.User` . `UseAuthentication`Veya çağrılmadan önce yöntemi çağırın `UseMvcWithDefaultRoute` `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts). Tanımlama bilgisi kimlik doğrulaması hakkında daha fazla bilgi için bkz <xref:security/authentication/cookie> ..

## <a name="apply-authorization"></a>Yetkilendirmeyi uygula

`AuthorizeAttribute`Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin. Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Oturumu kapat

Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağırın. Aşağıdaki kod, `Logout` *Dizin* sayfasına bir sayfa işleyicisi ekler:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin. Uygulama, `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` geri dönüş olarak kullanılır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
