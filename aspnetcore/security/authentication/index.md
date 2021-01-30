---
title: ASP.NET Core kimlik doğrulamasına genel bakış
author: mjrousos
description: ASP.NET Core kimlik doğrulaması hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 1/24/2021
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
uid: security/authentication/index
ms.openlocfilehash: 72036e9c4c92ee5dd82ac4a67e766fb0e5c8f924
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057297"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="26329-103">ASP.NET Core kimlik doğrulamasına genel bakış</span><span class="sxs-lookup"><span data-stu-id="26329-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="26329-104">, [Mike Rousos](https://github.com/mjrousos) tarafından</span><span class="sxs-lookup"><span data-stu-id="26329-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="26329-105">Kimlik doğrulaması, bir kullanıcının kimliğini belirleme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="26329-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="26329-106">[Yetkilendirme](xref:security/authorization/introduction) , bir kullanıcının bir kaynağa erişip erişemeyeceğini belirleme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="26329-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="26329-107">ASP.NET Core, kimlik doğrulaması, `IAuthenticationService` kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index)tarafından kullanılan tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="26329-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="26329-108">Kimlik doğrulama hizmeti, kimlik doğrulama ile ilgili eylemleri tamamlamaya yönelik kayıtlı kimlik doğrulama işleyicilerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="26329-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="26329-109">Kimlik doğrulaması ile ilgili eylemlere örnek olarak şunlar verilebilir:</span><span class="sxs-lookup"><span data-stu-id="26329-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="26329-110">Kullanıcı kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="26329-110">Authenticating a user.</span></span>
* <span data-ttu-id="26329-111">Kimliği doğrulanmamış bir Kullanıcı kısıtlı bir kaynağa erişmeyi denediğinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="26329-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="26329-112">Kayıtlı kimlik doğrulama işleyicileri ve yapılandırma seçenekleri "şemalar" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="26329-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="26329-113">Kimlik doğrulama düzenleri, kimlik doğrulama hizmetleri ' de kaydedilerek belirtilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26329-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="26329-114">Bir çağrısından sonra şemaya özgü genişletme yöntemini çağırarak `services.AddAuthentication` ( `AddJwtBearer` `AddCookie` Örneğin, veya gibi).</span><span class="sxs-lookup"><span data-stu-id="26329-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="26329-115">Bu uzantı yöntemleri, uygun ayarlarla şemaları kaydetmek için [Authenticationbuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="26329-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="26329-116">Daha seyrek, [Authenticationbuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) doğrudan çağırarak.</span><span class="sxs-lookup"><span data-stu-id="26329-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="26329-117">Örneğin, aşağıdaki kod, cookie ve JWT taşıyıcı kimlik doğrulama düzenlerini için kimlik doğrulama hizmetleri ve işleyiciler kaydeder:</span><span class="sxs-lookup"><span data-stu-id="26329-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="26329-118">`AddAuthentication`Parametresi, `JwtBearerDefaults.AuthenticationScheme` belirli bir şema istenmediğinde varsayılan olarak kullanılacak düzenin adıdır.</span><span class="sxs-lookup"><span data-stu-id="26329-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="26329-119">Birden çok düzen kullanılırsa, yetkilendirme ilkeleri (veya yetkilendirme öznitelikleri) kullanıcının kimliğini doğrulamak için bağımlı oldukları [kimlik doğrulama düzenini (veya düzenleri) belirtebilir](xref:security/authorization/limitingidentitybyscheme) .</span><span class="sxs-lookup"><span data-stu-id="26329-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="26329-120">Yukarıdaki örnekte, cookie kimlik doğrulama düzeni adı belirtilerek kullanılabilir ( `CookieAuthenticationDefaults.AuthenticationScheme` Varsayılan olarak, çağrılırken farklı bir ad sağlansa da `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="26329-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="26329-121">Bazı durumlarda, çağrısı `AddAuthentication` diğer uzantı yöntemleri tarafından otomatik olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="26329-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="26329-122">Örneğin, kullanırken [ASP.NET Core Identity](xref:security/authentication/identity) , dahili olarak `AddAuthentication` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="26329-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="26329-123">Kimlik doğrulama ara yazılımı, uygulamasının ' de `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> uzantı yöntemi çağırarak öğesine eklenir `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="26329-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="26329-124">Çağıran `UseAuthentication` , daha önce kaydedilen kimlik doğrulama düzenlerini kullanan ara yazılımı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="26329-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="26329-125">`UseAuthentication`Kimliği doğrulanan kullanıcılara bağlı olan herhangi bir ara yazılımın önüne çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="26329-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="26329-126">Endpoint Routing kullanılırken, çağrısının `UseAuthentication` gitmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="26329-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="26329-127">Ardından `UseRouting` , kimlik doğrulama kararlarında yol bilgileri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="26329-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="26329-128">Önce `UseEndpoints` , kullanıcıların, uç noktalara erişmeden önce kimlik doğrulaması yapılır.</span><span class="sxs-lookup"><span data-stu-id="26329-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="26329-129">Kimlik doğrulama kavramları</span><span class="sxs-lookup"><span data-stu-id="26329-129">Authentication Concepts</span></span>

<span data-ttu-id="26329-130">Kimlik doğrulama, <xref:System.Security.Claims.ClaimsPrincipal> izin kararlarını sağlamak için yetkilendirme sağlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="26329-130">Authentication is responsible for providing the <xref:System.Security.Claims.ClaimsPrincipal> for authorization to make permission decisions against.</span></span> <span data-ttu-id="26329-131">Doğru talepler kümesini oluşturmaktan sorumlu olan kimlik doğrulama işleyicisinin hangisi olduğunu seçmek için birden çok kimlik doğrulama düzeni yaklaşımına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="26329-131">There are multiple authentication scheme approaches to select which authentication handler is responsible for generating the correct set of claims:</span></span>

  * <span data-ttu-id="26329-132">Bir sonraki bölümde ele alınan [kimlik doğrulama düzeni](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="26329-132">[Authentication scheme](xref:security/authorization/limitingidentitybyscheme), also discussed in the next section.</span></span>
  * <span data-ttu-id="26329-133">Sonraki bölümde ele alınan varsayılan kimlik doğrulama düzeni.</span><span class="sxs-lookup"><span data-stu-id="26329-133">The default authentication scheme, discussed in the next section.</span></span>
  * <span data-ttu-id="26329-134">Doğrudan [HttpContext. User](xref:Microsoft.AspNetCore.Http.HttpContext.User)öğesini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="26329-134">Directly set [HttpContext.User](xref:Microsoft.AspNetCore.Http.HttpContext.User).</span></span>

<span data-ttu-id="26329-135">Şemaları otomatik olarak yoklama yoktur.</span><span class="sxs-lookup"><span data-stu-id="26329-135">There is no automatic probing of schemes.</span></span> <span data-ttu-id="26329-136">Varsayılan düzen belirtilmemişse, düzen Yetkilendir özniteliğinde belirtilmelidir, aksi takdirde aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="26329-136">If the default scheme is not specified, the scheme must be specified in the authorize attribute, otherwise, the following error is thrown:</span></span>

  <span data-ttu-id="26329-137">InvalidOperationException: authenticationScheme belirtilmedi ve hiçbir Defaultano planı bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="26329-137">InvalidOperationException: No authenticationScheme was specified, and there was no DefaultAuthenticateScheme found.</span></span> <span data-ttu-id="26329-138">Varsayılan düzenler Addaduthentication (dize defaultScheme) veya Addaduthentication (eylem &lt; authenticationoptions &gt; configureoptions) kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="26329-138">The default schemes can be set using either AddAuthentication(string defaultScheme) or AddAuthentication(Action&lt;AuthenticationOptions&gt; configureOptions).</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="26329-139">Kimlik doğrulama düzeni</span><span class="sxs-lookup"><span data-stu-id="26329-139">Authentication scheme</span></span>

<span data-ttu-id="26329-140">[Kimlik doğrulama düzeni](xref:security/authorization/limitingidentitybyscheme) , doğru talep kümesini oluşturmadan sorumlu olan kimlik doğrulama işleyicisini seçebilir.</span><span class="sxs-lookup"><span data-stu-id="26329-140">The [authentication scheme](xref:security/authorization/limitingidentitybyscheme) can select which authentication handler is responsible for generating the correct set of claims.</span></span> <span data-ttu-id="26329-141">Daha fazla bilgi için bkz. [belirli bir şemayla yetkilendirme](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="26329-141">For more information, see [Authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span>

<span data-ttu-id="26329-142">Kimlik doğrulama düzeni, öğesine karşılık gelen bir addır:</span><span class="sxs-lookup"><span data-stu-id="26329-142">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="26329-143">Bir kimlik doğrulama işleyicisi.</span><span class="sxs-lookup"><span data-stu-id="26329-143">An authentication handler.</span></span>
* <span data-ttu-id="26329-144">İşleyicinin belirli bir örneğini yapılandırma seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="26329-144">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="26329-145">Şemaları, ilişkili işleyicinin kimlik doğrulama, sınama ve fordeklarasyonu davranışlarına başvurma mekanizması olarak faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="26329-145">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="26329-146">Örneğin, bir yetkilendirme ilkesi, kullanıcının kimliğini doğrulamak için hangi kimlik doğrulama şemasının (veya düzenlerinin) kullanılması gerektiğini belirtmek için şema adlarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="26329-146">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="26329-147">Kimlik doğrulamasını yapılandırırken, varsayılan kimlik doğrulama düzenini belirlemek yaygın bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="26329-147">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="26329-148">Varsayılan şema, bir kaynak belirli bir düzeni istemediği takdirde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26329-148">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="26329-149">Şunları da mümkündür:</span><span class="sxs-lookup"><span data-stu-id="26329-149">It's also possible to:</span></span>

* <span data-ttu-id="26329-150">Kimlik doğrulama, sınama ve fordeklarasyon eylemleri için kullanılacak farklı varsayılan şemaları belirtin.</span><span class="sxs-lookup"><span data-stu-id="26329-150">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="26329-151">Birden çok düzeni [ilke düzenlerini](xref:security/authentication/policyschemes)kullanarak bir tek birleştirme.</span><span class="sxs-lookup"><span data-stu-id="26329-151">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="26329-152">Kimlik doğrulama işleyicisi</span><span class="sxs-lookup"><span data-stu-id="26329-152">Authentication handler</span></span>

<span data-ttu-id="26329-153">Kimlik doğrulama işleyicisi:</span><span class="sxs-lookup"><span data-stu-id="26329-153">An authentication handler:</span></span>

* <span data-ttu-id="26329-154">, Bir düzenin davranışını uygulayan bir türdür.</span><span class="sxs-lookup"><span data-stu-id="26329-154">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="26329-155">Veya ' den <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> türetilir <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="26329-155">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="26329-156">, Kullanıcıların kimliğini doğrulamak için Birincil sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="26329-156">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="26329-157">Kimlik doğrulama düzeninin yapılandırmasına ve gelen istek bağlamına göre, kimlik doğrulama işleyicileri:</span><span class="sxs-lookup"><span data-stu-id="26329-157">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="26329-158"><xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket>Kimlik doğrulaması başarılı olursa kullanıcının kimliğini temsil eden nesneleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="26329-158">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="26329-159">Kimlik doğrulaması başarısız olursa ' No Result ' veya ' Failure ' döndürün.</span><span class="sxs-lookup"><span data-stu-id="26329-159">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="26329-160">Kullanıcıların kaynaklara erişmeyi deneyeceği işlemler ve yasaklamaya yönelik yöntemlere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="26329-160">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="26329-161">Bunlara erişim yetkisi yoktur (fordeklarasyon).</span><span class="sxs-lookup"><span data-stu-id="26329-161">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="26329-162">Kimliği doğrulanmamış olduğunda (zorluk).</span><span class="sxs-lookup"><span data-stu-id="26329-162">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="26329-163">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="26329-163">Authenticate</span></span>

<span data-ttu-id="26329-164">Kimlik doğrulama düzeninin kimlik doğrulama eylemi, kullanıcının kimliğini istek bağlamına göre oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="26329-164">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="26329-165"><xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>Kimlik doğrulamanın başarılı olup olmadığını ve bu durumda kullanıcının kimlik doğrulama biletinde kimliğini belirten bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="26329-165">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="26329-166">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="26329-166">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="26329-167">Kimlik doğrulama örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="26329-167">Authenticate examples include:</span></span>

* <span data-ttu-id="26329-168">cookieKimlik doğrulama şeması, kullanıcının kimliğini s 'den oluşturan cookie .</span><span class="sxs-lookup"><span data-stu-id="26329-168">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="26329-169">Bir JWT taşıyıcı şeması, kullanıcının kimliğini oluşturmak için bir JWT taşıyıcı belirtecini seri durumdan çıkarma ve doğrulama.</span><span class="sxs-lookup"><span data-stu-id="26329-169">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="26329-170">Sınama</span><span class="sxs-lookup"><span data-stu-id="26329-170">Challenge</span></span>

<span data-ttu-id="26329-171">Kimliği doğrulanmamış bir kullanıcı kimlik doğrulaması gerektiren bir uç nokta istediğinde yetkilendirme ile kimlik doğrulama sınaması çağrılır.</span><span class="sxs-lookup"><span data-stu-id="26329-171">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="26329-172">Örneğin, anonim bir Kullanıcı kısıtlı bir kaynak istediğinde veya bir oturum açma bağlantısına tıkladığı zaman bir kimlik doğrulama sınaması verilir.</span><span class="sxs-lookup"><span data-stu-id="26329-172">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="26329-173">Yetkilendirme, belirtilen kimlik doğrulama düzenlerini kullanarak bir zorluk çağırır ya da hiçbiri belirtilmemişse varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="26329-173">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="26329-174">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="26329-174">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="26329-175">Kimlik doğrulama sınaması örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="26329-175">Authentication challenge examples include:</span></span>

* <span data-ttu-id="26329-176">cookieKullanıcıyı bir oturum açma sayfasına yönlendiren bir kimlik doğrulama düzeni.</span><span class="sxs-lookup"><span data-stu-id="26329-176">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="26329-177">Üst bilgiyle 401 sonucu döndüren bir JWT taşıyıcı şeması `www-authenticate: bearer` .</span><span class="sxs-lookup"><span data-stu-id="26329-177">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="26329-178">Bir sınama eylemi, kullanıcının istenen kaynağa erişmek için hangi kimlik doğrulama mekanizmasını kullanacağınızı bilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="26329-178">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="26329-179">Yasaklamaz</span><span class="sxs-lookup"><span data-stu-id="26329-179">Forbid</span></span>

<span data-ttu-id="26329-180">Kimliği doğrulanmış bir kullanıcı erişimine izin verilmeyen bir kaynağa erişmeyi denediğinde kimlik doğrulama düzeninin fordeklarasyon eylemi yetkilendirme tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="26329-180">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="26329-181">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="26329-181">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="26329-182">Kimlik doğrulama fordeklarasyon örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="26329-182">Authentication forbid examples include:</span></span>
* <span data-ttu-id="26329-183">Bir cookie kimlik doğrulama şeması, kullanıcıyı erişimi belirten bir sayfaya yeniden yönlendirdi.</span><span class="sxs-lookup"><span data-stu-id="26329-183">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="26329-184">403 sonucunu döndüren bir JWT taşıyıcı şeması.</span><span class="sxs-lookup"><span data-stu-id="26329-184">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="26329-185">Özel bir kimlik doğrulama şeması, kullanıcının kaynağa erişim isteyebileceği bir sayfaya yeniden yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="26329-185">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="26329-186">Bir fordeklarasyon eylemi, kullanıcının şunları bilmesini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="26329-186">A forbid action can let the user know:</span></span>

* <span data-ttu-id="26329-187">Bunlar doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="26329-187">They are authenticated.</span></span>
* <span data-ttu-id="26329-188">İstenen kaynağa erişmelerine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="26329-188">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="26329-189">Challenge ve fordeklarasyon arasındaki farklılıklar için aşağıdaki bağlantılara bakın:</span><span class="sxs-lookup"><span data-stu-id="26329-189">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="26329-190">[İşletimsel kaynak Işleyicisiyle Challenge ve fordeklarasyonu](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="26329-190">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="26329-191">[Çekişme ve fordeklarasyon arasındaki farklar](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="26329-191">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="26329-192">Kiracı başına kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="26329-192">Authentication providers per tenant</span></span>

<span data-ttu-id="26329-193">ASP.NET Core Framework 'te çok kiracılı kimlik doğrulaması için yerleşik bir çözüm yoktur.</span><span class="sxs-lookup"><span data-stu-id="26329-193">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="26329-194">Müşterilerin, yerleşik özellikleri kullanarak bir tane yazabilmesi kesinlikle mümkün olsa da, müşterilerin bu amaçla daha fazla [çekirdeğe](https://www.orchardcore.net/) bakmalarını öneririz.</span><span class="sxs-lookup"><span data-stu-id="26329-194">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="26329-195">Orchard Core:</span><span class="sxs-lookup"><span data-stu-id="26329-195">Orchard Core is:</span></span>

* <span data-ttu-id="26329-196">ASP.NET Core ile oluşturulan açık kaynaklı modüler ve çok kiracılı bir uygulama çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="26329-196">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="26329-197">Bu uygulama çerçevesinin üzerine inşa edilen bir içerik yönetim sistemi (CMS).</span><span class="sxs-lookup"><span data-stu-id="26329-197">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="26329-198">Kiracı başına kimlik doğrulama sağlayıcılarının bir örneği için bkz. [Orchard Core](https://github.com/OrchardCMS/OrchardCore) kaynağı.</span><span class="sxs-lookup"><span data-stu-id="26329-198">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26329-199">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="26329-199">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="26329-200">Genel olarak kimliği doğrulanmış kullanıcılar gerektir</span><span class="sxs-lookup"><span data-stu-id="26329-200">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)
* [<span data-ttu-id="26329-201">Birden çok kimlik doğrulama şeması kullanma hakkında GitHub sorunu</span><span class="sxs-lookup"><span data-stu-id="26329-201">GitHub issue on using multiple authentication schemes</span></span>](https://github.com/dotnet/aspnetcore/issues/26002)
