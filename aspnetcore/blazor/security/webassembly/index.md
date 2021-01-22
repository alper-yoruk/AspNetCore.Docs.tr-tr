---
title: Güvenli ASP.NET Core Blazor WebAssembly
author: guardrex
description: BlazorWebassemlby uygulamalarını tek sayfalı uygulamalar (maça 'lar) olarak güvenli hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 2df938f3ace47472536020f9848e954fc4446f15
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658592"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="77673-103">Güvenli ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="77673-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="77673-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="77673-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="77673-105">Blazor WebAssembly uygulamalar, tek sayfalı uygulamalarla (maça 'Lar) aynı şekilde güvenli hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="77673-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="77673-106">Kullanıcıların maça üzerinde kimlik doğrulaması için birkaç yaklaşım vardır, ancak en yaygın ve kapsamlı yaklaşım, [OpenID Connect (OıDC)](https://openid.net/connect/)gibi [OAuth 2,0 protokolüne](https://oauth.net/)dayalı bir uygulama kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="77673-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="77673-107">Kimlik doğrulaması kitaplığı</span><span class="sxs-lookup"><span data-stu-id="77673-107">Authentication library</span></span>

<span data-ttu-id="77673-108">Blazor WebAssembly , kitaplık aracılığıyla OıDC kullanarak uygulamalara kimlik doğrulama ve yetkilendirme işlemini destekler [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) .</span><span class="sxs-lookup"><span data-stu-id="77673-108">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="77673-109">Kitaplığı ASP.NET Core arka uçlara karşı sorunsuz kimlik doğrulama için bir dizi temel sağlar.</span><span class="sxs-lookup"><span data-stu-id="77673-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="77673-110">Kitaplık, ASP.NET Core Identity [ Identity sunucu](https://identityserver.io/)üzerinde oluşturulan API yetkilendirme desteğiyle tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77673-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="77673-111">Kitaplık, Identity OpenID sağlayıcıları (OP) olarak adlandırılan OIDC 'yi destekleyen herhangi bir üçüncü taraf sağlayıcıya (IP) kimlik doğrulaması yapabilir.</span><span class="sxs-lookup"><span data-stu-id="77673-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="77673-112">İçindeki kimlik doğrulama desteği, Blazor WebAssembly `oidc-client.js` temeldeki kimlik doğrulama protokolü ayrıntılarını işlemek için kullanılan kitaplığın üzerine kurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="77673-112">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="77673-113">Benzer site s kullanımı gibi, maça 'Lar için kimlik doğrulama seçenekleri mevcuttur cookie .</span><span class="sxs-lookup"><span data-stu-id="77673-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="77673-114">Ancak, ' nin mühendislik tasarımı, Blazor WebAssembly uygulamalarda kimlik doğrulaması için en iyi seçenek olarak OAuth ve OıDC üzerinde kapatılmıştır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="77673-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="77673-115">JSON Web belirteçlerini temel alan [belirteç tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#token-based-authentication) [(jwts)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , işlevsel ve güvenlik nedenleriyle [ cookie tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#cookie-based-authentication) için seçilmiştir:</span><span class="sxs-lookup"><span data-stu-id="77673-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="77673-116">Belirteç tabanlı bir protokol kullanmak, belirteçler tüm isteklerde gönderilmediğinden daha küçük bir saldırı yüzeyi alanı sunar.</span><span class="sxs-lookup"><span data-stu-id="77673-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="77673-117">Belirteçleri açıkça gönderildiğinden, sunucu uç noktaları [siteler arası Istek sahteciliği (CSRF)](xref:security/anti-request-forgery) için koruma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="77673-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="77673-118">Bu, Blazor WebAssembly MVC veya Pages uygulamalarıyla birlikte uygulamaları barındırmanıza olanak tanır Razor .</span><span class="sxs-lookup"><span data-stu-id="77673-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="77673-119">Belirteçlerin şundan daha dar izinleri var cookie .</span><span class="sxs-lookup"><span data-stu-id="77673-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="77673-120">Örneğin, belirteçler Kullanıcı hesabını yönetmek ya da bu işlevsellik açık bir şekilde uygulanmadığı takdirde kullanıcının parolasını değiştirmek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="77673-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="77673-121">Belirteçler, varsayılan olarak bir saat, saldırı penceresini sınırlayan kısa bir yaşam süresine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="77673-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="77673-122">Belirteçler de herhangi bir zamanda iptal edilebilir.</span><span class="sxs-lookup"><span data-stu-id="77673-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="77673-123">İstemci ve sunucu için kimlik doğrulama işlemiyle ilgili olarak kendi içinde bulunan JWTs teklifi garantisi.</span><span class="sxs-lookup"><span data-stu-id="77673-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="77673-124">Örneğin, bir istemci, aldığı belirteçlerin meşru olduğunu ve belirli bir kimlik doğrulama işleminin bir parçası olarak yayıldığını tespit etmek ve doğrulamak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="77673-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="77673-125">Üçüncü bir taraf, kimlik doğrulama işleminin ortasında bir belirteci geçirmeyi denerse, istemci anahtarlamalı belirteci algılayabilir ve kullanmaktan kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="77673-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="77673-126">OAuth ve OıDC içeren belirteçler, uygulamanın güvenli olduğundan emin olmak için doğru şekilde davranan kullanıcı aracısına güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="77673-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="77673-127">OAuth ve OıDC gibi belirteç tabanlı protokoller aynı güvenlik özellikleriyle barındırılan ve tek başına uygulamaların kimliğini doğrulamaya ve yetkilendirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="77673-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="77673-128">OıDC ile kimlik doğrulama işlemi</span><span class="sxs-lookup"><span data-stu-id="77673-128">Authentication process with OIDC</span></span>

<span data-ttu-id="77673-129">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)Kitaplık, OıDC kullanarak kimlik doğrulaması ve yetkilendirme uygulamak için çeşitli temel olanaklar sunar.</span><span class="sxs-lookup"><span data-stu-id="77673-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="77673-130">Büyük şartlar altında, kimlik doğrulaması aşağıdaki gibi çalışmaktadır:</span><span class="sxs-lookup"><span data-stu-id="77673-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="77673-131">Anonim Kullanıcı oturum açma düğmesini seçtiğinde veya özniteliği uygulanmış bir sayfa istediğinde [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) , kullanıcı uygulamanın oturum açma sayfasına ( `/authentication/login` ) yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="77673-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="77673-132">Oturum açma sayfasında, kimlik doğrulama kitaplığı yetkilendirme uç noktasına yeniden yönlendirme hazırlar.</span><span class="sxs-lookup"><span data-stu-id="77673-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="77673-133">Yetkilendirme uç noktası, Blazor WebAssembly uygulamanın dışındadır ve ayrı bir kaynak üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="77673-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="77673-134">Uç nokta, kullanıcının kimliğinin yapılıp yapılmayacağını ve yanıt olarak bir veya daha fazla belirteç vermeyi belirlemekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="77673-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="77673-135">Kimlik doğrulama kitaplığı, kimlik doğrulama yanıtını almak için bir oturum açma geri araması sağlar.</span><span class="sxs-lookup"><span data-stu-id="77673-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="77673-136">Kullanıcının kimliği doğrulanmadıysa, Kullanıcı, genellikle temel alınan kimlik doğrulama sistemine yönlendirilir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="77673-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="77673-137">Kullanıcının kimliği zaten doğrulandıktan sonra, yetkilendirme uç noktası uygun belirteçleri oluşturur ve tarayıcıyı, oturum açma geri çağırma uç noktasına () yeniden yönlendirir `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="77673-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="77673-138">Uygulama, Blazor WebAssembly oturum açma geri çağırma uç noktasını ( `/authentication/login-callback` ) yüklediğinde, kimlik doğrulama yanıtı işlenir.</span><span class="sxs-lookup"><span data-stu-id="77673-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="77673-139">Kimlik doğrulama işlemi başarıyla tamamlanırsa, kullanıcının kimliği doğrulanır ve isteğe bağlı olarak kullanıcının istediği özgün URL 'ye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="77673-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="77673-140">Kimlik doğrulama işlemi herhangi bir nedenle başarısız olursa, Kullanıcı oturum açma başarısız sayfasına ( `/authentication/login-failed` ) gönderilir ve bir hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="77673-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="77673-141">`Authentication` bileşeni</span><span class="sxs-lookup"><span data-stu-id="77673-141">`Authentication` component</span></span>

<span data-ttu-id="77673-142">`Authentication`Bileşen ( `Pages/Authentication.razor` ) uzak kimlik doğrulama işlemlerini işler ve uygulamanın şunları kullanmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="77673-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="77673-143">Kimlik doğrulama durumları için uygulama yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="77673-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="77673-144">Kimlik doğrulama durumları için Kullanıcı arabirimi içeriğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="77673-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="77673-145">Kimlik doğrulama durumunu yönetin.</span><span class="sxs-lookup"><span data-stu-id="77673-145">Manage authentication state.</span></span>

<span data-ttu-id="77673-146">Bir kullanıcıya kaydolma veya oturum açma gibi kimlik doğrulama eylemleri, Blazor Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> bileşenine geçirilir ve bu da kimlik doğrulama işlemlerinde durum denetimi devam eder.</span><span class="sxs-lookup"><span data-stu-id="77673-146">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="77673-147">Daha fazla bilgi ve örnek için bkz <xref:blazor/security/webassembly/additional-scenarios> ..</span><span class="sxs-lookup"><span data-stu-id="77673-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="77673-148">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="77673-148">Authorization</span></span>

<span data-ttu-id="77673-149">Blazor WebAssemblyUygulamalarda, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için, yetkilendirme denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="77673-149">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="77673-150">Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="77673-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="77673-151">**İstemci tarafı uygulamanız tarafından erişilen tüm API uç noktalarında sunucuda her zaman yetkilendirme denetimleri gerçekleştirin.**</span><span class="sxs-lookup"><span data-stu-id="77673-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="77673-152">Tüm uygulama için yetkilendirme iste</span><span class="sxs-lookup"><span data-stu-id="77673-152">Require authorization for the entire app</span></span>

<span data-ttu-id="77673-153">Aşağıdaki yaklaşımlardan birini kullanarak, [ `[Authorize]` özniteliği](xref:blazor/security/index#authorize-attribute) ([API belgeleri](xref:System.Web.Mvc.AuthorizeAttribute)) Razor uygulamanın her bileşenine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77673-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="77673-154">[`@attribute`](xref:mvc/views/razor#attribute)Dosyadaki yönergeyi kullanın `_Imports.razor` :</span><span class="sxs-lookup"><span data-stu-id="77673-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="77673-155">Özniteliği klasöründeki her bileşene ekleyin Razor `Pages` .</span><span class="sxs-lookup"><span data-stu-id="77673-155">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="77673-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType>İle ilke olarak ayarlanması <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> desteklenmez. </span><span class="sxs-lookup"><span data-stu-id="77673-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="77673-157">Belirteçleri Yenile</span><span class="sxs-lookup"><span data-stu-id="77673-157">Refresh tokens</span></span>

<span data-ttu-id="77673-158">Yenileme belirteçleri uygulamalarda istemci tarafında güvenli olamaz Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="77673-158">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="77673-159">Bu nedenle, doğrudan kullanım için yenileme belirteçleri uygulamaya gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="77673-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="77673-160">Yenileme belirteçleri, Blazor WebAssembly üçüncü taraf API 'lerine erişmek Için barındırılan bir çözümde sunucu tarafı uygulama tarafından korunabilir ve kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77673-160">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="77673-161">Daha fazla bilgi için bkz. <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span><span class="sxs-lookup"><span data-stu-id="77673-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="77673-162">Kullanıcılar için talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="77673-162">Establish claims for users</span></span>

<span data-ttu-id="77673-163">Uygulamalar, genellikle bir sunucuya bir Web API çağrısını temel alan kullanıcılar için talepler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77673-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="77673-164">Örneğin, Talepler genellikle bir uygulamada [Yetkilendirme kurmak](xref:blazor/security/index#authorization) için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77673-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="77673-165">Bu senaryolarda, uygulama hizmete erişmek için bir erişim belirteci ister ve bu belirteci kullanarak talepler için Kullanıcı verilerini elde eder.</span><span class="sxs-lookup"><span data-stu-id="77673-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="77673-166">Örnekler için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="77673-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="77673-167">Ek senaryolar: kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="77673-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="77673-168">Sunucu ile Linux üzerinde Azure App Service Identity</span><span class="sxs-lookup"><span data-stu-id="77673-168">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="77673-169">Sunucu ile Linux üzerinde Azure App Service dağıtım sırasında sertifikayı vereni açıkça belirtin Identity .</span><span class="sxs-lookup"><span data-stu-id="77673-169">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="77673-170">Daha fazla bilgi için bkz. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="77673-170">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="implementation-guidance"></a><span data-ttu-id="77673-171">Uygulama Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="77673-171">Implementation guidance</span></span>

<span data-ttu-id="77673-172">Bu *genel bakışın* altındaki makaleler, Blazor WebAssembly uygulamalarda kullanıcılara belirli sağlayıcılara göre kimlik doğrulaması hakkında bilgiler sağlar.</span><span class="sxs-lookup"><span data-stu-id="77673-172">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="77673-173">Tek başına Blazor WebAssembly uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="77673-173">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="77673-174">OıDC sağlayıcıları ve WebAssembly kimlik doğrulama kitaplığı için genel kılavuz</span><span class="sxs-lookup"><span data-stu-id="77673-174">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="77673-175">Microsoft Hesapları</span><span class="sxs-lookup"><span data-stu-id="77673-175">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="77673-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="77673-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="77673-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="77673-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="77673-178">Barındırılan Blazor WebAssembly uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="77673-178">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="77673-179">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="77673-179">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="77673-180">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="77673-180">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="77673-181">Identity Server</span><span class="sxs-lookup"><span data-stu-id="77673-181">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="77673-182">Aşağıdaki makalelerde daha fazla yapılandırma kılavuzu bulunur:</span><span class="sxs-lookup"><span data-stu-id="77673-182">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="77673-183">Daha fazla yapılandırma kılavuzu için bkz <xref:blazor/security/webassembly/additional-scenarios> ..</span><span class="sxs-lookup"><span data-stu-id="77673-183">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="77673-184">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77673-184">Additional resources</span></span>

* <span data-ttu-id="77673-185"><xref:host-and-deploy/proxy-load-balancer>: Üzerine yönergeler içerir:</span><span class="sxs-lookup"><span data-stu-id="77673-185"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="77673-186">WAN sunucularında ve iç ağlarda HTTPS şema bilgilerini korumak için Iletilen üstbilgiler ara yazılımı kullanma.</span><span class="sxs-lookup"><span data-stu-id="77673-186">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="77673-187">El ile düzen yapılandırması, doğru istek yönlendirme için istek yolu değişiklikleri ve Linux ve IIS olmayan ters proxy 'ler için istek düzenini iletme dahil ek senaryolar ve kullanım örnekleri.</span><span class="sxs-lookup"><span data-stu-id="77673-187">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
