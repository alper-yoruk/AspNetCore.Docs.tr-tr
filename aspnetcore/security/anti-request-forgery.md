---
title: ASP.NET Core siteler arası Istek sahteciliği (XSRF/CSRF) saldırılarını önle
author: steve-smith
description: Kötü amaçlı bir Web sitesinin istemci tarayıcısı ile uygulama arasındaki etkileşimi etkileyebilecek Web uygulamalarına karşı saldırıları nasıl önleyebileceğiniz hakkında daha fazla öğrenin.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: 197954965ee57b2a44ad0217d79ba142114e7df6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060852"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="54c28-103">ASP.NET Core siteler arası Istek sahteciliği (XSRF/CSRF) saldırılarını önle</span><span class="sxs-lookup"><span data-stu-id="54c28-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="54c28-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [fiyaz hasan](https://twitter.com/FiyazBinHasan)ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="54c28-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="54c28-105">Siteler arası istek sahteciliği (XSRF veya CSRF olarak da bilinir), kötü amaçlı bir Web uygulamasının, bir istemci tarayıcısı ile bu tarayıcıya güvenen bir Web uygulaması arasındaki etkileşimi etkileyebilecek Web 'de barındırılan uygulamalara karşı bir saldırıdır.</span><span class="sxs-lookup"><span data-stu-id="54c28-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="54c28-106">Web tarayıcıları her bir Web sitesi isteğiyle otomatik olarak bazı kimlik doğrulama belirteçleri gönderdikleri için bu saldırılar mümkündür.</span><span class="sxs-lookup"><span data-stu-id="54c28-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="54c28-107">Bu güvenlik düzeyi, *tek tıklamayla saldırı* veya *oturum* adı olarak da bilinir çünkü saldırı, kullanıcının önceden kimliği doğrulanmış oturumunun avantajlarından yararlanır.</span><span class="sxs-lookup"><span data-stu-id="54c28-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="54c28-108">CSRF saldırılarına bir örnek:</span><span class="sxs-lookup"><span data-stu-id="54c28-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="54c28-109">Kullanıcı, `www.good-banking-site.com` Forms kimlik doğrulaması kullanarak oturum açar.</span><span class="sxs-lookup"><span data-stu-id="54c28-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="54c28-110">Sunucu, kullanıcının kimliğini doğrular ve kimlik doğrulaması içeren bir yanıt yayınlar :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-110">The server authenticates the user and issues a response that includes an authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-111">Site, geçerli bir kimlik doğrulamasıyla aldığı herhangi bir isteğe güvendiğinden, saldırıya karşı savunmasız olur :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication :::no-loc(cookie):::.</span></span>
1. <span data-ttu-id="54c28-112">Kullanıcı kötü amaçlı bir siteyi ziyaret ettiğinde `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="54c28-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="54c28-113">Kötü amaçlı site, `www.bad-crook-site.com` aşağıdakine benzer BIR HTML formu içerir:</span><span class="sxs-lookup"><span data-stu-id="54c28-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="54c28-114">Formun `action` kötü amaçlı siteye değil, güvenlik açığı bulunan siteye gönderdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="54c28-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="54c28-115">Bu, CSRF 'nin "siteler arası" parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="54c28-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="54c28-116">Kullanıcı Gönder düğmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="54c28-116">The user selects the submit button.</span></span> <span data-ttu-id="54c28-117">Tarayıcı isteği yapar ve :::no-loc(cookie)::: istenen etki alanı için kimlik doğrulamasını otomatik olarak ekler `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="54c28-117">The browser makes the request and automatically includes the authentication :::no-loc(cookie)::: for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="54c28-118">İstek, `www.good-banking-site.com` kullanıcının kimlik doğrulama bağlamıyla sunucuda çalışır ve kimliği doğrulanmış bir kullanıcının gerçekleştirmesine izin verilen herhangi bir eylemi gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="54c28-119">Kullanıcının formu göndermek için düğmeyi seçtiği senaryoya ek olarak, kötü amaçlı site şunları verebilir:</span><span class="sxs-lookup"><span data-stu-id="54c28-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="54c28-120">Formu otomatik olarak gönderen bir komut dosyası çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="54c28-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="54c28-121">Form gönderimini bir AJAX isteği olarak gönderin.</span><span class="sxs-lookup"><span data-stu-id="54c28-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="54c28-122">Formu CSS kullanarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="54c28-122">Hide the form using CSS.</span></span>

<span data-ttu-id="54c28-123">Bu alternatif senaryolar, ilk olarak kötü amaçlı siteyi ziyaret eden kullanıcıdan herhangi bir eylem veya giriş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="54c28-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="54c28-124">HTTPS kullanmak CSRF saldırılarına engel olmaz.</span><span class="sxs-lookup"><span data-stu-id="54c28-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="54c28-125">Kötü amaçlı site, `https://www.good-banking-site.com/` güvenli olmayan bir istek gönderebilmesini mümkün olduğunca kolay bir şekilde gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="54c28-126">Bazı saldırılar, GET isteklerine yanıt veren uç noktaları, bu durumda eylemi gerçekleştirmek için bir resim etiketi kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="54c28-127">Bu saldırı biçimi, görüntülere izin veren ancak JavaScript 'ı engelleyen Forum sitelerinde yaygındır.</span><span class="sxs-lookup"><span data-stu-id="54c28-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="54c28-128">Değişkenlerin veya kaynakların değiştirildiği, GET isteklerinde durumu değiştiren uygulamalar kötü niyetli saldırılara açıktır.</span><span class="sxs-lookup"><span data-stu-id="54c28-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="54c28-129">**Durumu değiştirme isteklerini güvenli olmayan GET istekleri. Bir GET isteğindeki durumu hiçbir şekilde değiştirmemek en iyi uygulamadır.**</span><span class="sxs-lookup"><span data-stu-id="54c28-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="54c28-130">Şu nedenle kimlik doğrulama için kullanılan Web uygulamalarına CSRF saldırıları mümkündür :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="54c28-130">CSRF attacks are possible against web apps that use :::no-loc(cookie):::s for authentication because:</span></span>

* <span data-ttu-id="54c28-131">:::no-loc(cookie):::Bir Web uygulaması tarafından verilen tarayıcılar mağazası öğeleri.</span><span class="sxs-lookup"><span data-stu-id="54c28-131">Browsers store :::no-loc(cookie):::s issued by a web app.</span></span>
* <span data-ttu-id="54c28-132">Depolanan :::no-loc(cookie)::: :::no-loc(cookie)::: Kullanıcılar, kimliği doğrulanmış kullanıcılar için oturum içerir.</span><span class="sxs-lookup"><span data-stu-id="54c28-132">Stored :::no-loc(cookie):::s include session :::no-loc(cookie):::s for authenticated users.</span></span>
* <span data-ttu-id="54c28-133">Tarayıcılar, :::no-loc(cookie)::: uygulama isteğinin tarayıcıdan oluşturulma şeklinden bağımsız olarak her istekte bir etki alanı ile ilişkili tüm s 'leri Web uygulamasına gönderir.</span><span class="sxs-lookup"><span data-stu-id="54c28-133">Browsers send all of the :::no-loc(cookie):::s associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="54c28-134">Ancak, CSRF saldırıları, ' ı kötüye ile sınırlı değildir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-134">However, CSRF attacks aren't limited to exploiting :::no-loc(cookie):::s.</span></span> <span data-ttu-id="54c28-135">Örneğin, temel ve Özet kimlik doğrulaması da savunmasız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="54c28-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="54c28-136">Bir Kullanıcı temel veya Özet kimlik doğrulamasıyla oturum açtıktan sonra, oturum sona erene kadar tarayıcı kimlik bilgilerini otomatik olarak gönderir &dagger; .</span><span class="sxs-lookup"><span data-stu-id="54c28-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="54c28-137">&dagger;Bu bağlamda *oturum* , kullanıcının kimlik doğrulaması sırasında istemci tarafı oturumu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="54c28-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="54c28-138">Sunucu tarafı oturumları veya [ASP.NET Core oturum ara yazılımı](xref:fundamentals/app-state)ile ilgisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="54c28-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="54c28-139">Kullanıcılar, önlemler alarak CSRF güvenlik açıklarına karşı koruma sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="54c28-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="54c28-140">Web Apps 'in kullanımı bittiğinde oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="54c28-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="54c28-141">:::no-loc(cookie):::Düzenli aralıklarla tarayıcı öğeleri temizleyin.</span><span class="sxs-lookup"><span data-stu-id="54c28-141">Clear browser :::no-loc(cookie):::s periodically.</span></span>

<span data-ttu-id="54c28-142">Ancak, CSRF güvenlik açıkları son kullanıcıdan değil, Web uygulamasıyla ilgili bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="54c28-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="54c28-143">Kimlik doğrulamasının temelleri</span><span class="sxs-lookup"><span data-stu-id="54c28-143">Authentication fundamentals</span></span>

<span data-ttu-id="54c28-144">:::no-loc(Cookie):::tabanlı kimlik doğrulaması, popüler bir kimlik doğrulama biçimidir.</span><span class="sxs-lookup"><span data-stu-id="54c28-144">:::no-loc(Cookie):::-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="54c28-145">Belirteç tabanlı kimlik doğrulama sistemleri, özellikle tek sayfalı uygulamalar (maça 'Lar) için popülerlik olarak büyüyordur.</span><span class="sxs-lookup"><span data-stu-id="54c28-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="54c28-146">:::no-loc(Cookie):::tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="54c28-146">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="54c28-147">Bir Kullanıcı Kullanıcı adını ve parolasını kullanarak kimliğini doğruladığında, kimlik doğrulama ve yetkilendirme için kullanılabilecek bir kimlik doğrulama bileti içeren bir belirteç verilirler.</span><span class="sxs-lookup"><span data-stu-id="54c28-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="54c28-148">Belirteç, :::no-loc(cookie)::: istemcinin yaptığı her istekte eşlik eden bir olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="54c28-148">The token is stored as a :::no-loc(cookie)::: that accompanies every request the client makes.</span></span> <span data-ttu-id="54c28-149">Bu işlemi oluşturmak ve doğrulamak :::no-loc(cookie)::: , :::no-loc(Cookie)::: kimlik doğrulama ara yazılımı tarafından gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-149">Generating and validating this :::no-loc(cookie)::: is performed by the :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="54c28-150">[Ara yazılım](xref:fundamentals/middleware/index) , bir Kullanıcı sorumlusunu şifreli olarak serileştirir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-151">Sonraki isteklerde, ara yazılım öğesini doğrular :::no-loc(cookie)::: , sorumluyu yeniden [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)oluşturur ve sorumluyu [Kullanıcı](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="54c28-151">On subsequent requests, the middleware validates the :::no-loc(cookie):::, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="54c28-152">Belirteç tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="54c28-152">Token-based authentication</span></span>

<span data-ttu-id="54c28-153">Bir kullanıcının kimliği doğrulandığında, bunlar bir belirteç (antibir belirteç değil) olarak verilirler.</span><span class="sxs-lookup"><span data-stu-id="54c28-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="54c28-154">Belirteç, [talepler](/dotnet/framework/security/claims-based-identity-model) formundaki Kullanıcı bilgilerini veya uygulamayı uygulamada tutulan Kullanıcı durumuna işaret eden bir başvuru belirtecini içerir.</span><span class="sxs-lookup"><span data-stu-id="54c28-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="54c28-155">Bir kullanıcı kimlik doğrulaması gerektiren bir kaynağa erişmeyi denediğinde, belirteç, taşıyıcı belirteç biçiminde ek bir yetkilendirme üstbilgisiyle uygulamaya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="54c28-156">Bu, uygulamanın durum bilgisiz olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="54c28-156">This makes the app stateless.</span></span> <span data-ttu-id="54c28-157">Sonraki her istekte, belirteç sunucu tarafı doğrulama isteğine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="54c28-158">Bu belirteç *şifrelenmez* ; *kodlandı* .</span><span class="sxs-lookup"><span data-stu-id="54c28-158">This token isn't *encrypted* ; it's *encoded* .</span></span> <span data-ttu-id="54c28-159">Sunucusunda, bilgilerine erişmek için belirtecin kodu çözülür.</span><span class="sxs-lookup"><span data-stu-id="54c28-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="54c28-160">Sonraki isteklere belirteç göndermek için, belirteci tarayıcının yerel deposunda depolayın.</span><span class="sxs-lookup"><span data-stu-id="54c28-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="54c28-161">Belirteç tarayıcının yerel deposunda depolanıyorsa, CSRF güvenlik açığıyla ilgilenmeyin.</span><span class="sxs-lookup"><span data-stu-id="54c28-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="54c28-162">CSRF, belirteç bir içinde depolandığında sorun teşkil ediyor :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-162">CSRF is a concern when the token is stored in a :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-163">Daha fazla bilgi için bkz. GitHub sorunu [Spa kodu örneği iki :::no-loc(cookie)::: s ekler](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="54c28-163">For more information, see the GitHub issue [SPA code sample adds two :::no-loc(cookie):::s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="54c28-164">Tek bir etki alanında barındırılan birden çok uygulama</span><span class="sxs-lookup"><span data-stu-id="54c28-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="54c28-165">Paylaşılan barındırma ortamları, oturum ele geçirme, oturum açma CSRF ve diğer saldırılara karşı savunmasız kalır.</span><span class="sxs-lookup"><span data-stu-id="54c28-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="54c28-166">`example1.contoso.net`Ve `example2.contoso.net` farklı konaklar olsa da, etki alanı altındaki konaklar arasında örtülü bir güven ilişkisi vardır `*.contoso.net` .</span><span class="sxs-lookup"><span data-stu-id="54c28-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="54c28-167">Bu örtük güven ilişkisi, güvenilir olmayan ana bilgisayarların birbirini etkilemesine olanak tanır :::no-loc(cookie)::: (AJAX isteklerini yöneten aynı kaynak ILKELERI http s için de geçerlidir :::no-loc(cookie)::: ).</span><span class="sxs-lookup"><span data-stu-id="54c28-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's :::no-loc(cookie):::s (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP :::no-loc(cookie):::s).</span></span>

<span data-ttu-id="54c28-168">:::no-loc(cookie):::Aynı etki alanı üzerinde barındırılan uygulamalar arasında güvenilir bir şekilde yararlanan saldırılar, etki alanları paylaşılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-168">Attacks that exploit trusted :::no-loc(cookie):::s between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="54c28-169">Her uygulama kendi etki alanında barındırıldığı zaman, :::no-loc(cookie)::: açıktan yararlanılabilmesi için örtük güven ilişkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="54c28-169">When each app is hosted on its own domain, there is no implicit :::no-loc(cookie)::: trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="54c28-170">ASP.NET Core antiforgery yapılandırması</span><span class="sxs-lookup"><span data-stu-id="54c28-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="54c28-171">ASP.NET Core, [ASP.NET Core veri korumasını](xref:security/data-protection/introduction)kullanarak antiforgery uygular.</span><span class="sxs-lookup"><span data-stu-id="54c28-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="54c28-172">Veri koruma yığınının bir sunucu grubunda çalışacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="54c28-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="54c28-173">Daha fazla bilgi için bkz. [veri korumayı yapılandırma](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="54c28-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54c28-174">Aşağıdaki API 'lerden biri çağrıldığında, antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="54c28-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54c28-175">' De çağrıldığında, antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="54c28-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="54c28-176">ASP.NET Core 2,0 veya sonraki sürümlerde [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="54c28-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="54c28-177">Bir dosyada bulunan aşağıdaki biçimlendirme, :::no-loc(Razor)::: antiforgery belirteçlerini otomatik olarak oluşturur:</span><span class="sxs-lookup"><span data-stu-id="54c28-177">The following markup in a :::no-loc(Razor)::: file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="54c28-178">Benzer şekilde, [ıhtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) , formun yöntemi get değilse, varsayılan olarak antiforgery belirteçleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="54c28-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="54c28-179">HTML form öğeleri için antiforgery belirteçlerinin otomatik olarak oluşturulması, `<form>` etiketi `method="post"` özniteliği içerdiğinde ve aşağıdakilerden biri doğru olduğunda gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="54c28-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="54c28-180">Action özniteliği boş ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="54c28-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="54c28-181">Eylem özniteliği sağlanmadı ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="54c28-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="54c28-182">HTML form öğeleri için antiforgery belirteçlerinin otomatik nesli devre dışı bırakılabilir:</span><span class="sxs-lookup"><span data-stu-id="54c28-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="54c28-183">Özniteliği ile antiforgery belirteçlerini açıkça devre dışı bırakın `asp-antiforgery` :</span><span class="sxs-lookup"><span data-stu-id="54c28-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="54c28-184">Form öğesi etiket Yardımcısı! geri alma simgesi kullanılarak etiket yardımcılarını [kabul](xref:mvc/views/tag-helpers/intro#opt-out)etmedi:</span><span class="sxs-lookup"><span data-stu-id="54c28-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="54c28-185">Görünümden ' i kaldırın `FormTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="54c28-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="54c28-186">, `FormTagHelper` Şu yönergeyi görünüme ekleyerek bir görünümden kaldırılabilir :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="54c28-186">The `FormTagHelper` can be removed from a view by adding the following directive to the :::no-loc(Razor)::: view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="54c28-187">[ :::no-loc(Razor)::: Sayfalar](xref:razor-pages/index) , XSRF/CSRF 'den otomatik olarak korunur.</span><span class="sxs-lookup"><span data-stu-id="54c28-187">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="54c28-188">Daha fazla bilgi için bkz. [XSRF/CSRF ve :::no-loc(Razor)::: Sayfalar](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="54c28-188">For more information, see [XSRF/CSRF and :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="54c28-189">CSRF saldırılarına karşı savunma için en yaygın yaklaşım, *Eşitleyici belirteç deseninin* (STP) kullanılması.</span><span class="sxs-lookup"><span data-stu-id="54c28-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="54c28-190">Kullanıcı form verileri içeren bir sayfa istediğinde STP kullanılır:</span><span class="sxs-lookup"><span data-stu-id="54c28-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="54c28-191">Sunucu, geçerli kullanıcının kimliğiyle ilişkili bir belirteci istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="54c28-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="54c28-192">İstemci doğrulama için belirteci sunucuya geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="54c28-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="54c28-193">Sunucu kimliği doğrulanmış kullanıcının kimliğiyle eşleşmeyen bir belirteç alırsa istek reddedilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="54c28-194">Belirteç benzersizdir ve tahmin edilemez.</span><span class="sxs-lookup"><span data-stu-id="54c28-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="54c28-195">Belirteç Ayrıca, bir dizi isteğin doğru sıralamasını sağlamak için de kullanılabilir (örneğin,: sayfa 1 > sayfa 2 > sayfa 3).</span><span class="sxs-lookup"><span data-stu-id="54c28-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="54c28-196">ASP.NET Core MVC ve sayfa şablonlarındaki tüm formlar, :::no-loc(Razor)::: antiforgery belirteçleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="54c28-196">All of the forms in ASP.NET Core MVC and :::no-loc(Razor)::: Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="54c28-197">Aşağıdaki görünüm örnekleri, antiforgery belirteçleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="54c28-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="54c28-198">`<form>`HTML Yardımcısı Ile etiket yardımcıları kullanmadan bir öğeye açık bir şekilde antiforgery belirteci ekleyin [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="54c28-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="54c28-199">Yukarıdaki durumların her birinde, ASP.NET Core şuna benzer bir gizli form alanı ekler:</span><span class="sxs-lookup"><span data-stu-id="54c28-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="54c28-200">ASP.NET Core, antiforgery belirteçleriyle çalışmak için üç [filtre](xref:mvc/controllers/filters) içerir:</span><span class="sxs-lookup"><span data-stu-id="54c28-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="54c28-201">Validateantiforgeryıtoken</span><span class="sxs-lookup"><span data-stu-id="54c28-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="54c28-202">Oto Validateantiforgeryıtoken</span><span class="sxs-lookup"><span data-stu-id="54c28-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="54c28-203">Ignoreantiforgeri belirteci</span><span class="sxs-lookup"><span data-stu-id="54c28-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="54c28-204">Antiforgery seçenekleri</span><span class="sxs-lookup"><span data-stu-id="54c28-204">Antiforgery options</span></span>

<span data-ttu-id="54c28-205">İçindeki [antiforgery seçeneklerini](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="54c28-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set :::no-loc(Cookie)::: properties using :::no-loc(Cookie):::Builder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="54c28-206">&dagger;`:::no-loc(Cookie):::` [ :::no-loc(Cookie)::: Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) sınıfının özelliklerini kullanarak antiforgery özelliklerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="54c28-206">&dagger;Set the antiforgery `:::no-loc(Cookie):::` properties using the properties of the [:::no-loc(Cookie):::Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) class.</span></span>

| <span data-ttu-id="54c28-207">Seçenek</span><span class="sxs-lookup"><span data-stu-id="54c28-207">Option</span></span> | <span data-ttu-id="54c28-208">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54c28-208">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="54c28-209">Antiforgery 'leri oluşturmak için kullanılan ayarları belirler :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-209">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| [<span data-ttu-id="54c28-210">Form alanadı</span><span class="sxs-lookup"><span data-stu-id="54c28-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="54c28-211">Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı.</span><span class="sxs-lookup"><span data-stu-id="54c28-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="54c28-212">HeaderName</span><span class="sxs-lookup"><span data-stu-id="54c28-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="54c28-213">Antiforgery sistemi tarafından kullanılan üstbilginin adı.</span><span class="sxs-lookup"><span data-stu-id="54c28-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="54c28-214">Varsa `null` , sistem yalnızca form verilerini dikkate alır.</span><span class="sxs-lookup"><span data-stu-id="54c28-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="54c28-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="54c28-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="54c28-216">Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="54c28-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="54c28-217">Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="54c28-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="54c28-218">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="54c28-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.:::no-loc(Cookie):::Domain = "contoso.com";
    options.:::no-loc(Cookie):::Name = "X-CSRF-TOKEN-COOKIENAME";
    options.:::no-loc(Cookie):::Path = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="54c28-219">Seçenek</span><span class="sxs-lookup"><span data-stu-id="54c28-219">Option</span></span> | <span data-ttu-id="54c28-220">Açıklama</span><span class="sxs-lookup"><span data-stu-id="54c28-220">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="54c28-221">Antiforgery 'leri oluşturmak için kullanılan ayarları belirler :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-221">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| <span data-ttu-id="54c28-222">[:::no-loc(Cookie):::Etki alanı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span><span class="sxs-lookup"><span data-stu-id="54c28-222">[:::no-loc(Cookie):::Domain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span></span> | <span data-ttu-id="54c28-223">Öğesinin etki alanı :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-223">The domain of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-224">Varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="54c28-224">Defaults to `null`.</span></span> <span data-ttu-id="54c28-225">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="54c28-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="54c28-226">Önerilen alternatif şunlardır :::no-loc(Cookie)::: . Alanını.</span><span class="sxs-lookup"><span data-stu-id="54c28-226">The recommended alternative is :::no-loc(Cookie):::.Domain.</span></span> |
| <span data-ttu-id="54c28-227">[:::no-loc(Cookie):::Ad](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span><span class="sxs-lookup"><span data-stu-id="54c28-227">[:::no-loc(Cookie):::Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span></span> | <span data-ttu-id="54c28-228">:::no-loc(cookie)::: öğesinin adı.</span><span class="sxs-lookup"><span data-stu-id="54c28-228">The name of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-229">Ayarlanmamışsa, sistem [varsayılan :::no-loc(Cookie)::: ön eki](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (") ile başlayan benzersiz bir ad oluşturur. AspNetCore. Antiforgery. ").</span><span class="sxs-lookup"><span data-stu-id="54c28-229">If not set, the system generates a unique name beginning with the [Default:::no-loc(Cookie):::Prefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="54c28-230">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="54c28-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="54c28-231">Önerilen alternatif şunlardır :::no-loc(Cookie)::: . Ada.</span><span class="sxs-lookup"><span data-stu-id="54c28-231">The recommended alternative is :::no-loc(Cookie):::.Name.</span></span> |
| <span data-ttu-id="54c28-232">[:::no-loc(Cookie):::Yol](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span><span class="sxs-lookup"><span data-stu-id="54c28-232">[:::no-loc(Cookie):::Path](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span></span> | <span data-ttu-id="54c28-233">Üzerinde ayarlanan yol :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-233">The path set on the :::no-loc(cookie):::.</span></span> <span data-ttu-id="54c28-234">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="54c28-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="54c28-235">Önerilen alternatif şunlardır :::no-loc(Cookie)::: . Yolun.</span><span class="sxs-lookup"><span data-stu-id="54c28-235">The recommended alternative is :::no-loc(Cookie):::.Path.</span></span> |
| [<span data-ttu-id="54c28-236">Form alanadı</span><span class="sxs-lookup"><span data-stu-id="54c28-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="54c28-237">Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı.</span><span class="sxs-lookup"><span data-stu-id="54c28-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="54c28-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="54c28-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="54c28-239">Antiforgery sistemi tarafından kullanılan üstbilginin adı.</span><span class="sxs-lookup"><span data-stu-id="54c28-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="54c28-240">Varsa `null` , sistem yalnızca form verilerini dikkate alır.</span><span class="sxs-lookup"><span data-stu-id="54c28-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="54c28-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="54c28-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="54c28-242">Antiforgery sistemi için HTTPS 'nin gerekli olup olmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="54c28-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="54c28-243">`true`, Https olmayan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="54c28-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="54c28-244">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="54c28-244">Defaults to `false`.</span></span> <span data-ttu-id="54c28-245">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="54c28-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="54c28-246">Önerilen alternatif ayarlanır :::no-loc(Cookie)::: . SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="54c28-246">The recommended alternative is to set :::no-loc(Cookie):::.SecurePolicy.</span></span> |
| [<span data-ttu-id="54c28-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="54c28-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="54c28-248">Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="54c28-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="54c28-249">Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="54c28-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="54c28-250">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="54c28-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="54c28-251">Daha fazla bilgi için bkz. [ :::no-loc(Cookie)::: authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="54c28-251">For more information, see [:::no-loc(Cookie):::AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="54c28-252">Iantiforgery ile antiforgery özelliklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="54c28-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="54c28-253">[Iantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) , antiforgery özelliklerini YAPıLANDıRMAK için API sağlar.</span><span class="sxs-lookup"><span data-stu-id="54c28-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="54c28-254">`IAntiforgery` , `Configure` sınıfının yönteminde istenebilir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="54c28-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="54c28-255">Aşağıdaki örnek, bir antiforgery belirteci oluşturmak ve yanıt olarak :::no-loc(cookie)::: (Bu konunun ilerleyen kısımlarında açıklanan varsayılan angular adlandırma kuralını kullanarak) olarak yanıtta göndermek için uygulamanın giriş sayfasından ara yazılım kullanır:</span><span class="sxs-lookup"><span data-stu-id="54c28-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a :::no-loc(cookie)::: (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="54c28-256">Antiforgery doğrulaması gerektir</span><span class="sxs-lookup"><span data-stu-id="54c28-256">Require antiforgery validation</span></span>

<span data-ttu-id="54c28-257">[Validateantiforgeritoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) , tek bir eyleme, denetleyiciye veya küresel olarak uygulanabilen bir eylem filtresidir.</span><span class="sxs-lookup"><span data-stu-id="54c28-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="54c28-258">Bu filtre uygulanmış eylemlere yapılan istekler, istek geçerli bir antiforgery belirteci içermiyorsa engellenir.</span><span class="sxs-lookup"><span data-stu-id="54c28-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="54c28-259">`ValidateAntiForgeryToken`Öznitelik, http get istekleri de dahil olmak üzere, işaret eden eylem yöntemlerine istek için bir belirteç gerektirir.</span><span class="sxs-lookup"><span data-stu-id="54c28-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="54c28-260">Öznitelik, `ValidateAntiForgeryToken` uygulamanın denetleyicileri arasında uygulanırsa, özniteliğiyle geçersiz kılınabilir `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="54c28-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="54c28-261">ASP.NET Core, istekleri otomatik olarak almak için antiforgery belirteçleri eklemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="54c28-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="54c28-262">Yalnızca güvenli olmayan HTTP metotları için antiforgery belirteçlerini otomatik olarak doğrula</span><span class="sxs-lookup"><span data-stu-id="54c28-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="54c28-263">ASP.NET Core uygulamalar güvenli HTTP yöntemleri (GET, HEAD, OPTIONS ve TRACE) için antiforgery belirteçleri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="54c28-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="54c28-264">Özniteliği genel olarak uygulamak `ValidateAntiForgeryToken` ve ardından özniteliklerle geçersiz kılmak yerine `IgnoreAntiforgeryToken` , [oto Validateantiforgerontoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) özniteliği kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="54c28-265">Bu öznitelik, özniteliğiyle aynı şekilde çalışır `ValidateAntiForgeryToken` , ancak AŞAĞıDAKI http yöntemlerini kullanarak yapılan isteklere belirteç gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="54c28-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="54c28-266">GET</span><span class="sxs-lookup"><span data-stu-id="54c28-266">GET</span></span>
* <span data-ttu-id="54c28-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="54c28-267">HEAD</span></span>
* <span data-ttu-id="54c28-268">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="54c28-268">OPTIONS</span></span>
* <span data-ttu-id="54c28-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="54c28-269">TRACE</span></span>

<span data-ttu-id="54c28-270">`AutoValidateAntiforgeryToken`API olmayan senaryolara yönelik olarak kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="54c28-271">Bu, varsayılan olarak POST eylemlerinin korunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="54c28-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="54c28-272">Diğer bir deyişle, `ValidateAntiForgeryToken` tek tek eylem yöntemlerine uygulanmamışsa, varsayılan olarak antiforgery belirteçlerini yok saymanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="54c28-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="54c28-273">Bu senaryoda, bir POST eylemi yönteminin korumasız olarak bırakılması, uygulamanın CSRF saldırılarına karşı savunmasız bırakılması daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="54c28-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="54c28-274">Tüm gönderimler, antiforgery belirtecini göndermelidir.</span><span class="sxs-lookup"><span data-stu-id="54c28-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="54c28-275">API 'lerin, belirtecin parçası olmayan bir otomatik mekanizması yoktur :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-275">APIs don't have an automatic mechanism for sending the non-:::no-loc(cookie)::: part of the token.</span></span> <span data-ttu-id="54c28-276">Uygulama, büyük olasılıkla istemci kodu uygulamasına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="54c28-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="54c28-277">Bazı örnekler aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="54c28-277">Some examples are shown below:</span></span>

<span data-ttu-id="54c28-278">Sınıf düzeyi örnek:</span><span class="sxs-lookup"><span data-stu-id="54c28-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="54c28-279">Genel örnek:</span><span class="sxs-lookup"><span data-stu-id="54c28-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54c28-280">servislere. AddMvc (Options => seçenekleri. Filters. Add (New, oto Validateantiforgeryıtokenattribute ()));</span><span class="sxs-lookup"><span data-stu-id="54c28-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="54c28-281">Küresel veya denetleyici antiforgery özniteliklerini geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="54c28-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="54c28-282">[Ignoreantiforgeri Token](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtresi, belirli bir eyleme (veya denetleyiciye) yönelik bir antiforgery belirtecinin gereksinimini ortadan kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="54c28-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="54c28-283">Uygulandığında, bu filtre `ValidateAntiForgeryToken` ve `AutoValidateAntiforgeryToken` daha yüksek düzeyde (genel olarak veya bir denetleyicide) belirtilen filtreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="54c28-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="54c28-284">Kimlik doğrulamasından sonra belirteçleri Yenile</span><span class="sxs-lookup"><span data-stu-id="54c28-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="54c28-285">Kullanıcının kimliği doğrulandıktan sonra, Kullanıcı bir görünüm veya sayfalar sayfasına yönlendirildikten sonra belirteçlerin yenilenmesi gerekir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or :::no-loc(Razor)::: Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="54c28-286">JavaScript, AJAX ve maça</span><span class="sxs-lookup"><span data-stu-id="54c28-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="54c28-287">Geleneksel HTML tabanlı uygulamalarda, antiforgery belirteçleri gizli form alanları kullanılarak sunucuya geçirilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="54c28-288">Modern JavaScript tabanlı uygulamalar ve maça 'Lar, programlama yoluyla birçok istek yapılır.</span><span class="sxs-lookup"><span data-stu-id="54c28-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="54c28-289">Bu AJAX istekleri, belirteci göndermek için diğer teknikleri (istek üstbilgileri veya :::no-loc(cookie)::: öğeleri gibi) kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-289">These AJAX requests may use other techniques (such as request headers or :::no-loc(cookie):::s) to send the token.</span></span>

<span data-ttu-id="54c28-290">:::no-loc(cookie):::S kimlik doğrulama belirteçlerini depolamak ve SUNUCUDAKI API isteklerinin kimliğini doğrulamak için kullanılıyorsa, CSRF olası bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="54c28-290">If :::no-loc(cookie):::s are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="54c28-291">Yerel depolama, belirteci depolamak için kullanılıyorsa, yerel depolama alanındaki değerler her istek ile sunucuya otomatik olarak gönderilmediğinden CSRF güvenlik açığı azaltılabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="54c28-292">Bu nedenle, istemci üzerinde antiforgery belirtecini depolamak ve belirteci istek üst bilgisi olarak göndermek için yerel depolama kullanmak önerilen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="54c28-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="54c28-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="54c28-293">JavaScript</span></span>

<span data-ttu-id="54c28-294">Görünümler ile JavaScript kullanarak, belirteç, görünümün içinden bir hizmet kullanılarak oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="54c28-295">[Microsoft. AspNetCore. antiforgery. ıantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) hizmetini görünüme ekleyin ve [Getandstorebelirteçlerini](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)çağırın:</span><span class="sxs-lookup"><span data-stu-id="54c28-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="54c28-296">Bu yaklaşım, sunucudan ayarları doğrudan veya istemciden okurken doğrudan anlaşma gereksinimini ortadan kaldırır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="54c28-296">This approach eliminates the need to deal directly with setting :::no-loc(cookie):::s from the server or reading them from the client.</span></span>

<span data-ttu-id="54c28-297">Önceki örnekte, AJAX GÖNDERI üst bilgisinin gizli alan değerini okumak için JavaScript kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="54c28-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="54c28-298">JavaScript Ayrıca, içindeki belirteçlere erişebilir :::no-loc(cookie)::: ve belirtecinin, :::no-loc(cookie)::: belirtecin değeri ile bir üst bilgi oluşturmak için içeriğini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-298">JavaScript can also access tokens in :::no-loc(cookie):::s and use the :::no-loc(cookie):::'s contents to create a header with the token's value.</span></span>

```csharp
context.Response.:::no-loc(Cookie):::s.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options { HttpOnly = false });
```

<span data-ttu-id="54c28-299">Çağrılan bir üst bilgide belirteç göndermek için komut dosyası isteklerinin kabul edilmediği varsayılarak `X-CSRF-TOKEN` , bir üst bilgiyi aramak için antiforgery hizmetini yapılandırın `X-CSRF-TOKEN` :</span><span class="sxs-lookup"><span data-stu-id="54c28-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="54c28-300">Aşağıdaki örnek, uygun üst bilgiyle bir AJAX isteği oluşturmak için JavaScript kullanır:</span><span class="sxs-lookup"><span data-stu-id="54c28-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function get:::no-loc(Cookie):::(cname) {
    var name = cname + "=";
    var decoded:::no-loc(Cookie)::: = decodeURIComponent(document.:::no-loc(cookie):::);
    var ca = decoded:::no-loc(Cookie):::.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = get:::no-loc(Cookie):::("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="54c28-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="54c28-301">AngularJS</span></span>

<span data-ttu-id="54c28-302">AngularJS, CSRF adresine yönelik bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="54c28-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="54c28-303">Sunucu adıyla bir gönderirse :::no-loc(cookie)::: `XSRF-TOKEN` , AngularJS `$http` hizmeti :::no-loc(cookie)::: sunucuya bir istek gönderdiğinde değeri bir üst bilgiye ekler.</span><span class="sxs-lookup"><span data-stu-id="54c28-303">If the server sends a :::no-loc(cookie)::: with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the :::no-loc(cookie)::: value to a header when it sends a request to the server.</span></span> <span data-ttu-id="54c28-304">Bu işlem otomatiktir.</span><span class="sxs-lookup"><span data-stu-id="54c28-304">This process is automatic.</span></span> <span data-ttu-id="54c28-305">Üstbilginin istemcide açıkça ayarlanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="54c28-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="54c28-306">Üst bilgi adı `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="54c28-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="54c28-307">Sunucunun bu üstbilgiyi algılaması ve içeriğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="54c28-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="54c28-308">ASP.NET Core API 'nin uygulama başlangıcında bu kurala göre çalışması için:</span><span class="sxs-lookup"><span data-stu-id="54c28-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="54c28-309">Uygulamanızı, çağrılan bir belirteci sunacak şekilde yapılandırın :::no-loc(cookie)::: `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="54c28-309">Configure your app to provide a token in a :::no-loc(cookie)::: called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="54c28-310">Antiforgery hizmetini adlı üstbilgiyi aramak üzere yapılandırın `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="54c28-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="54c28-311">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54c28-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="54c28-312">Antiforgery 'yi uzat</span><span class="sxs-lookup"><span data-stu-id="54c28-312">Extend antiforgery</span></span>

<span data-ttu-id="54c28-313">[Iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) türü, geliştiricilerin her bir belirteçte ek verileri yuvarlak aşağı getirerek CSRF sisteminin davranışını genişletmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="54c28-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="54c28-314">[Getaddıtionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) yöntemi, bir alan belirtecinin oluşturulduğu her seferinde çağrılır ve döndürülen değer oluşturulan belirtecin içine gömülür.</span><span class="sxs-lookup"><span data-stu-id="54c28-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="54c28-315">Bir uygulayıcısı, bir zaman damgası, bir kerelik anahtar veya başka bir değer döndürebilir ve ardından, belirteç doğrulandığında bu verileri doğrulamak için [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) öğesini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="54c28-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="54c28-316">İstemcinin Kullanıcı adı, oluşturulan belirteçlere zaten eklenmiş olduğundan, bu bilgileri eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="54c28-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="54c28-317">Bir belirteç ek verileri içeriyorsa ancak `IAntiForgeryAdditionalDataProvider` yapılandırılmamışsa, ek veriler doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="54c28-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54c28-318">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="54c28-318">Additional resources</span></span>

* <span data-ttu-id="54c28-319">[Open Web Application Security projesinde](https://www.owasp.org/index.php/Main_Page) [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="54c28-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
