---
title: Güvenli ASP.NET Blazor Çekirdek WebAssembly
author: guardrex
description: WebAssemlby uygulamalarını Tek Sayfa Uygulamaları (SPA) olarak nasıl güvene Blazor aldığınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538980"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="b529b-103">Güvenli ASP.NET Blazor Çekirdek WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b529b-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="b529b-104">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="b529b-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="b529b-105">WebAssembly uygulamaları, Tek Sayfa uygulamaları (SPA' lar) ile aynı şekilde güvenli hale alınır.</span><span class="sxs-lookup"><span data-stu-id="b529b-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="b529b-106">Kullanıcıların SP'lere doğruluğunu doğrulamak için çeşitli yaklaşımlar vardır, ancak en yaygın ve kapsamlı yaklaşım, Açık Kimlik Bağlantısı [(OIDC)](https://openid.net/connect/)gibi [OAuth 2.0 protokolüne](https://oauth.net/)dayalı bir uygulama kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b529b-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="b529b-107">Kimlik doğrulama kitaplığı</span><span class="sxs-lookup"><span data-stu-id="b529b-107">Authentication library</span></span>

Blazor<span data-ttu-id="b529b-108">WebAssembly, kitaplık üzerinden OIDC kullanarak uygulamaların `Microsoft.AspNetCore.Components.WebAssembly.Authentication` doğrulanmasını ve yetkilendirilmesini destekler.</span><span class="sxs-lookup"><span data-stu-id="b529b-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="b529b-109">Kitaplık, ASP.NET Core arka uçlarına karşı sorunsuz bir şekilde doğrulanması için bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="b529b-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="b529b-110">Kitaplık, ASP.NET Çekirdek Kimlik'i [Identity Server'ın](https://identityserver.io/)üzerine inşa edilmiş API yetkilendirme desteğiyle bütünleştirir.</span><span class="sxs-lookup"><span data-stu-id="b529b-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="b529b-111">Kitaplık, OpenID Sağlayıcıları (OP) olarak adlandırılan OIDC'yi destekleyen herhangi bir üçüncü taraf Kimlik Sağlayıcısına (IP) karşı kimlik doğrulaması yapabilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="b529b-112">WebAssembly'deki Blazor kimlik doğrulama desteği, temel kimlik doğrulama iletişim kuralı ayrıntılarını işlemek için kullanılan *oidc-client.js* kitaplığı üzerine oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b529b-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="b529b-113">SameSite tanımlama bilgilerinin kullanımı gibi spa'ların doğrulamasını doğrulamak için başka seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="b529b-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="b529b-114">Ancak, WebAssembly'in Blazor mühendislik tasarımı, WebAssembly uygulamalarında Blazor kimlik doğrulama için en iyi seçenek olarak OAuth ve OIDC'ye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b529b-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="b529b-115">[JSON Web Belirteçleri 'ne (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) dayalı [belirteç tabanlı kimlik doğrulama,](xref:security/anti-request-forgery#token-based-authentication) işlevsel ve güvenlik nedenleriyle [çerez tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#cookie-based-authentication) üzerinden seçildi:</span><span class="sxs-lookup"><span data-stu-id="b529b-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="b529b-116">Belirteçtabanlı bir protokol kullanmak, belirteçler tüm isteklerde gönderilmeyin diye daha küçük bir saldırı yüzey alanı sunar.</span><span class="sxs-lookup"><span data-stu-id="b529b-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="b529b-117">Belirteçler açıkça gönderildiğinden, sunucu uç noktaları [Siteleri Arası İstek Sahteciliği'ne (CSRF)](xref:security/anti-request-forgery) karşı koruma gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="b529b-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="b529b-118">Bu, WebAssembly Blazor uygulamalarını MVC veya Razor sayfaları uygulamalarının yanında barındırmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b529b-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="b529b-119">Belirteçlerin tanımlama bilgilerinden daha dar izinleri vardır.</span><span class="sxs-lookup"><span data-stu-id="b529b-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="b529b-120">Örneğin, söz konusu işlev açıkça uygulanmadığı sürece belirteçler kullanıcı hesabını yönetmek veya kullanıcının parolasını değiştirmek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="b529b-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="b529b-121">Belirteçlerin, varsayılan olarak bir saatlik kısa bir kullanım ömrü vardır ve bu da saldırı penceresini sınırlar.</span><span class="sxs-lookup"><span data-stu-id="b529b-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="b529b-122">Jetonlar da herhangi bir zamanda iptal edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="b529b-123">Bağımsız JWT'ler, kimlik doğrulama işlemi yle ilgili istemciye ve sunucuya garanti ler sunar.</span><span class="sxs-lookup"><span data-stu-id="b529b-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="b529b-124">Örneğin, istemci, aldığı belirteçlerin meşru olduğunu ve belirli bir kimlik doğrulama işleminin bir parçası olarak yayımlandığını algılama ve doğrulama aracına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b529b-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="b529b-125">Üçüncü bir taraf kimlik doğrulama işleminin ortasında bir belirteci değiştirmeye çalışırsa, istemci anahtarlanan belirteci algılayabilir ve kullanmaktan kaçınabilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="b529b-126">OAuth ve OIDC ile belirteçler, uygulamanın güvenli olduğundan emin olmak için kullanıcı aracısının doğru şekilde davranmasına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="b529b-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="b529b-127">OAuth ve OIDC gibi belirteç tabanlı protokoller, barındırılan ve bağımsız uygulamaların aynı güvenlik özelliklerine sahip doğrulanmasına ve yetkilendirilmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b529b-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="b529b-128">OIDC ile kimlik doğrulama işlemi</span><span class="sxs-lookup"><span data-stu-id="b529b-128">Authentication process with OIDC</span></span>

<span data-ttu-id="b529b-129">Kitaplık, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC kullanarak kimlik doğrulama ve yetkilendirme yi uygulamak için birkaç ilkel uygulama sunar.</span><span class="sxs-lookup"><span data-stu-id="b529b-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="b529b-130">Genel anlamda, kimlik doğrulama aşağıdaki gibi çalışır:</span><span class="sxs-lookup"><span data-stu-id="b529b-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="b529b-131">Anonim bir kullanıcı giriş düğmesini seçtiğinde veya [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği uygulanan bir sayfa istediğinde, kullanıcı uygulamanın giriş`/authentication/login`sayfasına yönlendirilir ( ).</span><span class="sxs-lookup"><span data-stu-id="b529b-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="b529b-132">Giriş sayfasında, kimlik doğrulama kitaplığı yetkilendirme bitiş noktasına yeniden yönlendirmeye hazırlanır.</span><span class="sxs-lookup"><span data-stu-id="b529b-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="b529b-133">Yetkilendirme bitiş noktası WebAssembly Blazor uygulamasının dışındadır ve ayrı bir kaynaktan barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="b529b-134">Bitiş noktası, kullanıcının kimliğinin doğrulanıp doğrulanmadığını belirlemekten ve yanıt olarak bir veya daha fazla belirteç vermekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="b529b-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="b529b-135">Kimlik doğrulama kitaplığı, kimlik doğrulama yanıtını almak için bir oturum açma geri araması sağlar.</span><span class="sxs-lookup"><span data-stu-id="b529b-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="b529b-136">Kullanıcının kimliği doğrulanmıyorsa, kullanıcı genellikle Temel Kimlik ASP.NET olan temel kimlik doğrulama sistemine yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="b529b-137">Kullanıcı nın kimliği zaten doğrulanmışsa, yetkilendirme bitiş noktası uygun belirteçleri oluşturur ve tarayıcıyı giriş`/authentication/login-callback`geri arama bitiş noktasına geri yönlendirir ( ).</span><span class="sxs-lookup"><span data-stu-id="b529b-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="b529b-138">Blazor WebAssembly uygulaması oturum açma geri arama bitiş`/authentication/login-callback`noktasını yüklediğinde , kimlik doğrulama yanıtı işlenir.</span><span class="sxs-lookup"><span data-stu-id="b529b-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="b529b-139">Kimlik doğrulama işlemi başarıyla tamamlanırsa, kullanıcı kimlik doğrulaması yapılır ve isteğe bağlı olarak kullanıcının istediği orijinal korumalı URL'ye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="b529b-140">Kimlik doğrulama işlemi herhangi bir nedenle başarısız olursa, kullanıcı oturum`/authentication/login-failed`açma başarısız sayfasına gönderilir ve bir hata görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b529b-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="b529b-141">Kimlik doğrulama ile ön oluşturmayı destekleyin</span><span class="sxs-lookup"><span data-stu-id="b529b-141">Support prerendering with authentication</span></span>

<span data-ttu-id="b529b-142">Barındırılan Blazor WebAssembly uygulama konularından birinde yönergeleri takip ettikten sonra, aşağıdaki yönergeleri kullanarak aşağıdaki yönergeleri kullanarak aşağıdaki leri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="b529b-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="b529b-143">Yetkilendirme gerektirmeyen ön işleme yolları.</span><span class="sxs-lookup"><span data-stu-id="b529b-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="b529b-144">Yetkilendirmenin gerekli olduğu yolları önceden oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="b529b-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="b529b-145">İstemci uygulamasının `Program` sınıfında *(Program.cs),* ortak hizmet kayıtlarını ayrı bir `ConfigureCommonServices`yönteme (örneğin,) faktör:</span><span class="sxs-lookup"><span data-stu-id="b529b-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="b529b-146">Sunucu `Startup.ConfigureServices`uygulamasında, aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="b529b-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="b529b-147">Sunucu uygulamasının `Startup.Configure` yönteminde aşağıdakilerle değiştirin: `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`</span><span class="sxs-lookup"><span data-stu-id="b529b-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="b529b-148">Sunucu uygulamasında, yoksa bir *Sayfalar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b529b-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="b529b-149">Sunucu uygulamasının *Sayfalar* klasöründe *bir _Host.cshtml* sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b529b-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="b529b-150">İstemci uygulamasının *wwwroot/index.html* dosyasındaki içeriği *Pages/_Host.cshtml* dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="b529b-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="b529b-151">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b529b-151">Update the file's contents:</span></span>

* <span data-ttu-id="b529b-152">Dosyanın üstüne ekleyin. `@page "_Host"`</span><span class="sxs-lookup"><span data-stu-id="b529b-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="b529b-153">Etiketi `<app>Loading...</app>` aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b529b-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="b529b-154">Barındırılan uygulamalar ve üçüncü taraf giriş sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="b529b-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="b529b-155">Barındırılan Blazor bir WebAssembly uygulamasını üçüncü taraf bir sağlayıcıyla doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="b529b-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="b529b-156">Hangisini seçeceğiniz senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b529b-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="b529b-157">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="b529b-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="b529b-158">Yalnızca korumalı üçüncü taraf API'lerini aramak için kullanıcıları kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b529b-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="b529b-159">Üçüncü taraf API sağlayıcısına karşı istemci tarafı OAuth akışı ile kullanıcının doğrulamasını:</span><span class="sxs-lookup"><span data-stu-id="b529b-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="b529b-160">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="b529b-160">In this scenario:</span></span>

* <span data-ttu-id="b529b-161">Uygulamayı barındıran sunucu bir rol oynamaz.</span><span class="sxs-lookup"><span data-stu-id="b529b-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="b529b-162">Sunucudaki API'ler korunamıyor.</span><span class="sxs-lookup"><span data-stu-id="b529b-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="b529b-163">Uygulama yalnızca korumalı üçüncü taraf API'leri arayabilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="b529b-164">Üçüncü taraf sağlayıcıyla kullanıcıları doğrulave ana bilgisayar sunucusunda ve üçüncü tarafta korumalı API'leri arayın</span><span class="sxs-lookup"><span data-stu-id="b529b-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="b529b-165">Kimliği bir üçüncü taraf giriş sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b529b-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="b529b-166">Üçüncü taraf API erişimi için gerekli belirteçleri edinin ve bunları saklayın.</span><span class="sxs-lookup"><span data-stu-id="b529b-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="b529b-167">Bir kullanıcı oturum açtığınızda, Identity kimlik doğrulama işleminin bir parçası olarak erişim toplar ve belirteçleri yeniler.</span><span class="sxs-lookup"><span data-stu-id="b529b-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="b529b-168">Bu noktada, üçüncü taraf API'lere API çağrıları yapmak için birkaç yaklaşım mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b529b-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="b529b-169">Üçüncü taraf erişim jetonunu almak için sunucu erişim jetonunu kullanma</span><span class="sxs-lookup"><span data-stu-id="b529b-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="b529b-170">Sunucu API bitiş noktasından üçüncü taraf erişim belirteci almak için sunucuda oluşturulan erişim belirteci kullanın.</span><span class="sxs-lookup"><span data-stu-id="b529b-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="b529b-171">Buradan, üçüncü taraf API kaynaklarını doğrudan kimlikten istemciden aramak için üçüncü taraf erişim belirteci'ni kullanın.</span><span class="sxs-lookup"><span data-stu-id="b529b-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="b529b-172">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="b529b-172">We don't recommend this approach.</span></span> <span data-ttu-id="b529b-173">Bu yaklaşım, üçüncü taraf erişim belirteci bir ortak istemci için oluşturulmuş gibi ele gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b529b-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="b529b-174">OAuth açısından, genel uygulamanın bir istemci sırrı yoktur, çünkü sırları güvenli bir şekilde depolamak için güvenilir değildir ve erişim belirteci gizli bir istemci için üretilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="b529b-175">Gizli istemci, istemci sırrı olan ve sırları güvenli bir şekilde saklayabildiği varsayılan bir istemcidir.</span><span class="sxs-lookup"><span data-stu-id="b529b-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="b529b-176">Üçüncü taraf erişim belirteci, üçüncü tarafın daha güvenilir bir istemci için belirteci yaydığı gerçeğine dayanarak hassas işlemleri gerçekleştirmek için ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="b529b-177">Benzer şekilde, diğer kısıtlamalar konmadığı sürece istemciye sınırsız erişim sağladığından, belirteçleri güvenilir olmayan bir istemciye verilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b529b-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="b529b-178">Üçüncü taraf API'lerini aramak için istemciden sunucu API'sine API çağrıları yapma</span><span class="sxs-lookup"><span data-stu-id="b529b-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="b529b-179">İstemciden sunucu API'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="b529b-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="b529b-180">Sunucudan, üçüncü taraf API kaynağının erişim belirtecisini alın ve gerekli olan çağrıyı sorun.</span><span class="sxs-lookup"><span data-stu-id="b529b-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="b529b-181">Bu yaklaşım, üçüncü taraf API'yi çağırmak için sunucuüzerinden fazladan bir ağ atlama gerektirir, ancak sonuçta daha güvenli bir deneyim sağlar:</span><span class="sxs-lookup"><span data-stu-id="b529b-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="b529b-182">Sunucu yenileme belirteçlerini depolayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimini kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b529b-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="b529b-183">Uygulama, daha hassas izinler içerebilecek erişim belirteçlerini sunucudan sızdıramaz.</span><span class="sxs-lookup"><span data-stu-id="b529b-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
