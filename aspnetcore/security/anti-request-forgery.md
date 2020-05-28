---
<span data-ttu-id="26db8-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-102">'Blazor'</span></span>
- <span data-ttu-id="26db8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-103">'Identity'</span></span>
- <span data-ttu-id="26db8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-105">'Razor'</span></span>
- <span data-ttu-id="26db8-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="26db8-107">ASP.NET Core siteler arası Istek sahteciliği (XSRF/CSRF) saldırılarını önle</span><span class="sxs-lookup"><span data-stu-id="26db8-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="26db8-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [fiyaz hasan](https://twitter.com/FiyazBinHasan)ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="26db8-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="26db8-109">Siteler arası istek sahteciliği (XSRF veya CSRF olarak da bilinir), kötü amaçlı bir Web uygulamasının, bir istemci tarayıcısı ile bu tarayıcıya güvenen bir Web uygulaması arasındaki etkileşimi etkileyebilecek Web 'de barındırılan uygulamalara karşı bir saldırıdır.</span><span class="sxs-lookup"><span data-stu-id="26db8-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="26db8-110">Web tarayıcıları her bir Web sitesi isteğiyle otomatik olarak bazı kimlik doğrulama belirteçleri gönderdikleri için bu saldırılar mümkündür.</span><span class="sxs-lookup"><span data-stu-id="26db8-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="26db8-111">Bu güvenlik düzeyi, *tek tıklamayla saldırı* veya *oturum* adı olarak da bilinir çünkü saldırı, kullanıcının önceden kimliği doğrulanmış oturumunun avantajlarından yararlanır.</span><span class="sxs-lookup"><span data-stu-id="26db8-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="26db8-112">CSRF saldırılarına bir örnek:</span><span class="sxs-lookup"><span data-stu-id="26db8-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="26db8-113">Kullanıcı, `www.good-banking-site.com` Forms kimlik doğrulaması kullanarak oturum açar.</span><span class="sxs-lookup"><span data-stu-id="26db8-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="26db8-114">Sunucu, kullanıcının kimliğini doğrular ve kimlik doğrulama tanımlama bilgisi içeren bir yanıt yayınlar.</span><span class="sxs-lookup"><span data-stu-id="26db8-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="26db8-115">Site, geçerli bir kimlik doğrulama tanımlama bilgisiyle aldığı herhangi bir isteğe güvendiğinden saldırıya açıktır.</span><span class="sxs-lookup"><span data-stu-id="26db8-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="26db8-116">Kullanıcı kötü amaçlı bir siteyi ziyaret ettiğinde `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="26db8-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="26db8-117">Kötü amaçlı site, `www.bad-crook-site.com` aşağıdakine benzer BIR HTML formu içerir:</span><span class="sxs-lookup"><span data-stu-id="26db8-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="26db8-118">Formun `action` kötü amaçlı siteye değil, güvenlik açığı bulunan siteye gönderdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="26db8-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="26db8-119">Bu, CSRF 'nin "siteler arası" parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="26db8-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="26db8-120">Kullanıcı Gönder düğmesini seçer.</span><span class="sxs-lookup"><span data-stu-id="26db8-120">The user selects the submit button.</span></span> <span data-ttu-id="26db8-121">Tarayıcı isteği yapar ve istenen etki alanı için kimlik doğrulama tanımlama bilgisini otomatik olarak ekler `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="26db8-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="26db8-122">İstek, `www.good-banking-site.com` kullanıcının kimlik doğrulama bağlamıyla sunucuda çalışır ve kimliği doğrulanmış bir kullanıcının gerçekleştirmesine izin verilen herhangi bir eylemi gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="26db8-123">Kullanıcının formu göndermek için düğmeyi seçtiği senaryoya ek olarak, kötü amaçlı site şunları verebilir:</span><span class="sxs-lookup"><span data-stu-id="26db8-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="26db8-124">Formu otomatik olarak gönderen bir komut dosyası çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="26db8-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="26db8-125">Form gönderimini bir AJAX isteği olarak gönderin.</span><span class="sxs-lookup"><span data-stu-id="26db8-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="26db8-126">Formu CSS kullanarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="26db8-126">Hide the form using CSS.</span></span>

<span data-ttu-id="26db8-127">Bu alternatif senaryolar, ilk olarak kötü amaçlı siteyi ziyaret eden kullanıcıdan herhangi bir eylem veya giriş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="26db8-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="26db8-128">HTTPS kullanmak CSRF saldırılarına engel olmaz.</span><span class="sxs-lookup"><span data-stu-id="26db8-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="26db8-129">Kötü amaçlı site, `https://www.good-banking-site.com/` güvenli olmayan bir istek gönderebilmesini mümkün olduğunca kolay bir şekilde gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="26db8-130">Bazı saldırılar, GET isteklerine yanıt veren uç noktaları, bu durumda eylemi gerçekleştirmek için bir resim etiketi kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="26db8-131">Bu saldırı biçimi, görüntülere izin veren ancak JavaScript 'ı engelleyen Forum sitelerinde yaygındır.</span><span class="sxs-lookup"><span data-stu-id="26db8-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="26db8-132">Değişkenlerin veya kaynakların değiştirildiği, GET isteklerinde durumu değiştiren uygulamalar kötü niyetli saldırılara açıktır.</span><span class="sxs-lookup"><span data-stu-id="26db8-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="26db8-133">**Durumu değiştirme isteklerini güvenli olmayan GET istekleri. Bir GET isteğindeki durumu hiçbir şekilde değiştirmemek en iyi uygulamadır.**</span><span class="sxs-lookup"><span data-stu-id="26db8-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="26db8-134">CSRF saldırıları, kimlik doğrulaması için tanımlama bilgileri kullanan Web uygulamalarına karşı şu nedenle mümkündür:</span><span class="sxs-lookup"><span data-stu-id="26db8-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="26db8-135">Tarayıcılar bir Web uygulaması tarafından verilen tanımlama bilgilerini depolar.</span><span class="sxs-lookup"><span data-stu-id="26db8-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="26db8-136">Depolanan tanımlama bilgileri, kimliği doğrulanmış kullanıcılar için oturum tanımlama bilgilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="26db8-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="26db8-137">Tarayıcılar, bir etki alanı ile ilişkili tüm tanımlama bilgilerini, uygulama isteğinin tarayıcıdan oluşturulma şeklinden bağımsız olarak her istek için Web uygulamasına gönderir.</span><span class="sxs-lookup"><span data-stu-id="26db8-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="26db8-138">Ancak, CSRF saldırıları, tanımlama bilgilerini kötüye ile sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="26db8-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="26db8-139">Örneğin, temel ve Özet kimlik doğrulaması da savunmasız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="26db8-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="26db8-140">Bir Kullanıcı temel veya Özet kimlik doğrulamasıyla oturum açtıktan sonra, oturum sona erene kadar tarayıcı kimlik bilgilerini otomatik olarak gönderir &dagger; .</span><span class="sxs-lookup"><span data-stu-id="26db8-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="26db8-141">&dagger;Bu bağlamda *oturum* , kullanıcının kimlik doğrulaması sırasında istemci tarafı oturumu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="26db8-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="26db8-142">Sunucu tarafı oturumları veya [ASP.NET Core oturum ara yazılımı](xref:fundamentals/app-state)ile ilgisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="26db8-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="26db8-143">Kullanıcılar, önlemler alarak CSRF güvenlik açıklarına karşı koruma sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="26db8-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="26db8-144">Web Apps 'in kullanımı bittiğinde oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="26db8-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="26db8-145">Tarayıcı tanımlama bilgilerini düzenli aralıklarla temizleyin.</span><span class="sxs-lookup"><span data-stu-id="26db8-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="26db8-146">Ancak, CSRF güvenlik açıkları son kullanıcıdan değil, Web uygulamasıyla ilgili bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="26db8-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="26db8-147">Kimlik doğrulama temelleri</span><span class="sxs-lookup"><span data-stu-id="26db8-147">Authentication fundamentals</span></span>

<span data-ttu-id="26db8-148">Tanımlama bilgisi tabanlı kimlik doğrulama, popüler bir kimlik doğrulama biçimidir.</span><span class="sxs-lookup"><span data-stu-id="26db8-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="26db8-149">Belirteç tabanlı kimlik doğrulama sistemleri, özellikle tek sayfalı uygulamalar (maça 'Lar) için popülerlik olarak büyüyordur.</span><span class="sxs-lookup"><span data-stu-id="26db8-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="26db8-150">Tanımlama bilgisi tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="26db8-150">Cookie-based authentication</span></span>

<span data-ttu-id="26db8-151">Bir Kullanıcı Kullanıcı adını ve parolasını kullanarak kimliğini doğruladığında, kimlik doğrulama ve yetkilendirme için kullanılabilecek bir kimlik doğrulama bileti içeren bir belirteç verilirler.</span><span class="sxs-lookup"><span data-stu-id="26db8-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="26db8-152">Belirteç, istemcinin yaptığı her istekte eşlik eden bir tanımlama bilgisi olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="26db8-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="26db8-153">Bu tanımlama bilgisinin oluşturulması ve doğrulanması, tanımlama bilgisi kimlik doğrulama ara yazılımı tarafından gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="26db8-154">[Ara yazılım](xref:fundamentals/middleware/index) , bir Kullanıcı sorumlusunu şifreli bir tanımlama bilgisine seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="26db8-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="26db8-155">Sonraki isteklerde, ara yazılım tanımlama bilgisini doğrular, sorumluyu yeniden oluşturur ve sorumluyu [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext) [Kullanıcı](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="26db8-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="26db8-156">Belirteç tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="26db8-156">Token-based authentication</span></span>

<span data-ttu-id="26db8-157">Bir kullanıcının kimliği doğrulandığında, bunlar bir belirteç (antibir belirteç değil) olarak verilirler.</span><span class="sxs-lookup"><span data-stu-id="26db8-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="26db8-158">Belirteç, [talepler](/dotnet/framework/security/claims-based-identity-model) formundaki Kullanıcı bilgilerini veya uygulamayı uygulamada tutulan Kullanıcı durumuna işaret eden bir başvuru belirtecini içerir.</span><span class="sxs-lookup"><span data-stu-id="26db8-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="26db8-159">Bir kullanıcı kimlik doğrulaması gerektiren bir kaynağa erişmeyi denediğinde, belirteç, taşıyıcı belirteç biçiminde ek bir yetkilendirme üstbilgisiyle uygulamaya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="26db8-160">Bu, uygulamanın durum bilgisiz olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="26db8-160">This makes the app stateless.</span></span> <span data-ttu-id="26db8-161">Sonraki her istekte, belirteç sunucu tarafı doğrulama isteğine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="26db8-162">Bu belirteç *şifrelenmez*; *kodlandı*.</span><span class="sxs-lookup"><span data-stu-id="26db8-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="26db8-163">Sunucusunda, bilgilerine erişmek için belirtecin kodu çözülür.</span><span class="sxs-lookup"><span data-stu-id="26db8-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="26db8-164">Sonraki isteklere belirteç göndermek için, belirteci tarayıcının yerel deposunda depolayın.</span><span class="sxs-lookup"><span data-stu-id="26db8-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="26db8-165">Belirteç tarayıcının yerel deposunda depolanıyorsa, CSRF güvenlik açığıyla ilgilenmeyin.</span><span class="sxs-lookup"><span data-stu-id="26db8-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="26db8-166">CSRF, belirteç bir tanımlama bilgisinde depolandığında sorun teşkil ediyor.</span><span class="sxs-lookup"><span data-stu-id="26db8-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="26db8-167">Daha fazla bilgi için bkz. GitHub sorunu [Spa kodu örneği iki tanımlama bilgisi ekler](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="26db8-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="26db8-168">Tek bir etki alanında barındırılan birden çok uygulama</span><span class="sxs-lookup"><span data-stu-id="26db8-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="26db8-169">Paylaşılan barındırma ortamları, oturum ele geçirme, oturum açma CSRF ve diğer saldırılara karşı savunmasız kalır.</span><span class="sxs-lookup"><span data-stu-id="26db8-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="26db8-170">`example1.contoso.net`Ve `example2.contoso.net` farklı konaklar olsa da, etki alanı altındaki konaklar arasında örtülü bir güven ilişkisi vardır `*.contoso.net` .</span><span class="sxs-lookup"><span data-stu-id="26db8-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="26db8-171">Bu örtük güven ilişkisi, güvenilmeyen ana bilgisayarların birbirlerinin tanımlama bilgilerini etkilemesini sağlar (AJAX isteklerini yöneten aynı kaynak ilkeleri HTTP tanımlama bilgilerine uygulanmaz).</span><span class="sxs-lookup"><span data-stu-id="26db8-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="26db8-172">Aynı etki alanı üzerinde barındırılan uygulamalar arasında güvenilen tanımlama bilgileriyle faydalanan saldırılar, etki alanlarının paylaşılmasından engellenebilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="26db8-173">Her uygulama kendi etki alanında barındırıldığı zaman, açıktan yararlanılabilmesi için örtük bir tanımlama bilgisi güven ilişkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="26db8-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="26db8-174">ASP.NET Core antiforgery yapılandırması</span><span class="sxs-lookup"><span data-stu-id="26db8-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="26db8-175">ASP.NET Core, [ASP.NET Core veri korumasını](xref:security/data-protection/introduction)kullanarak antiforgery uygular.</span><span class="sxs-lookup"><span data-stu-id="26db8-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="26db8-176">Veri koruma yığınının bir sunucu grubunda çalışacak şekilde yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="26db8-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="26db8-177">Daha fazla bilgi için bkz. [veri korumayı yapılandırma](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="26db8-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26db8-178">Aşağıdaki API 'lerden biri çağrıldığında, antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26db8-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26db8-179">' De çağrıldığında, antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="26db8-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="26db8-180">ASP.NET Core 2,0 veya sonraki sürümlerde [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.</span><span class="sxs-lookup"><span data-stu-id="26db8-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="26db8-181">Bir dosyada bulunan aşağıdaki biçimlendirme, Razor antiforgery belirteçlerini otomatik olarak oluşturur:</span><span class="sxs-lookup"><span data-stu-id="26db8-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="26db8-182">Benzer şekilde, [ıhtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) , formun yöntemi get değilse, varsayılan olarak antiforgery belirteçleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="26db8-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="26db8-183">HTML form öğeleri için antiforgery belirteçlerinin otomatik olarak oluşturulması, `<form>` etiketi `method="post"` özniteliği içerdiğinde ve aşağıdakilerden biri doğru olduğunda gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="26db8-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="26db8-184">Action özniteliği boş ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="26db8-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="26db8-185">Eylem özniteliği sağlanmadı ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="26db8-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="26db8-186">HTML form öğeleri için antiforgery belirteçlerinin otomatik nesli devre dışı bırakılabilir:</span><span class="sxs-lookup"><span data-stu-id="26db8-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="26db8-187">Özniteliği ile antiforgery belirteçlerini açıkça devre dışı bırakın `asp-antiforgery` :</span><span class="sxs-lookup"><span data-stu-id="26db8-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="26db8-188">Form öğesi etiket Yardımcısı! geri alma simgesi kullanılarak etiket yardımcılarını [kabul](xref:mvc/views/tag-helpers/intro#opt-out)etmedi:</span><span class="sxs-lookup"><span data-stu-id="26db8-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="26db8-189">Görünümden ' i kaldırın `FormTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="26db8-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="26db8-190">, `FormTagHelper` Şu yönergeyi görünüme ekleyerek bir görünümden kaldırılabilir Razor :</span><span class="sxs-lookup"><span data-stu-id="26db8-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="26db8-191">[ Razor Sayfalar](xref:razor-pages/index) , XSRF/CSRF 'den otomatik olarak korunur.</span><span class="sxs-lookup"><span data-stu-id="26db8-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="26db8-192">Daha fazla bilgi için bkz. [XSRF/CSRF ve Razor Sayfalar](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="26db8-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="26db8-193">CSRF saldırılarına karşı savunma için en yaygın yaklaşım, *Eşitleyici belirteç deseninin* (STP) kullanılması.</span><span class="sxs-lookup"><span data-stu-id="26db8-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="26db8-194">Kullanıcı form verileri içeren bir sayfa istediğinde STP kullanılır:</span><span class="sxs-lookup"><span data-stu-id="26db8-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="26db8-195">Sunucu, geçerli kullanıcının kimliğiyle ilişkili bir belirteci istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="26db8-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="26db8-196">İstemci doğrulama için belirteci sunucuya geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="26db8-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="26db8-197">Sunucu kimliği doğrulanmış kullanıcının kimliğiyle eşleşmeyen bir belirteç alırsa istek reddedilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="26db8-198">Belirteç benzersizdir ve tahmin edilemez.</span><span class="sxs-lookup"><span data-stu-id="26db8-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="26db8-199">Belirteç Ayrıca, bir dizi isteğin doğru sıralamasını sağlamak için de kullanılabilir (örneğin,: sayfa 1 > sayfa 2 > sayfa 3).</span><span class="sxs-lookup"><span data-stu-id="26db8-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="26db8-200">ASP.NET Core MVC ve sayfa şablonlarındaki tüm formlar, Razor antiforgery belirteçleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="26db8-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="26db8-201">Aşağıdaki görünüm örnekleri, antiforgery belirteçleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="26db8-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="26db8-202">`<form>`HTML Yardımcısı Ile etiket yardımcıları kullanmadan bir öğeye açık bir şekilde antiforgery belirteci ekleyin [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="26db8-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="26db8-203">Yukarıdaki durumların her birinde, ASP.NET Core şuna benzer bir gizli form alanı ekler:</span><span class="sxs-lookup"><span data-stu-id="26db8-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="26db8-204">ASP.NET Core, antiforgery belirteçleriyle çalışmak için üç [filtre](xref:mvc/controllers/filters) içerir:</span><span class="sxs-lookup"><span data-stu-id="26db8-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="26db8-205">Validateantiforgeryıtoken</span><span class="sxs-lookup"><span data-stu-id="26db8-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="26db8-206">Oto Validateantiforgeryıtoken</span><span class="sxs-lookup"><span data-stu-id="26db8-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="26db8-207">Ignoreantiforgeri belirteci</span><span class="sxs-lookup"><span data-stu-id="26db8-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="26db8-208">Antiforgery seçenekleri</span><span class="sxs-lookup"><span data-stu-id="26db8-208">Antiforgery options</span></span>

<span data-ttu-id="26db8-209">İçindeki [antiforgery seçeneklerini](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26db8-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="26db8-210">&dagger;`Cookie`Using [ıebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) sınıfının özelliklerini kullanarak antiforgery özelliklerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="26db8-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="26db8-211">Seçenek</span><span class="sxs-lookup"><span data-stu-id="26db8-211">Option</span></span> | <span data-ttu-id="26db8-212">Açıklama</span><span class="sxs-lookup"><span data-stu-id="26db8-212">Description</span></span> |
| ---
<span data-ttu-id="26db8-213">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-214">'Blazor'</span></span>
- <span data-ttu-id="26db8-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-215">'Identity'</span></span>
- <span data-ttu-id="26db8-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-217">'Razor'</span></span>
- <span data-ttu-id="26db8-218">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-218">'SignalR' uid:</span></span> 

<span data-ttu-id="26db8-219">--- | ---title: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-220">'Blazor'</span></span>
- <span data-ttu-id="26db8-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-221">'Identity'</span></span>
- <span data-ttu-id="26db8-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-223">'Razor'</span></span>
- <span data-ttu-id="26db8-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="26db8-225">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-226">'Blazor'</span></span>
- <span data-ttu-id="26db8-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-227">'Identity'</span></span>
- <span data-ttu-id="26db8-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-229">'Razor'</span></span>
- <span data-ttu-id="26db8-230">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="26db8-231">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-232">'Blazor'</span></span>
- <span data-ttu-id="26db8-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-233">'Identity'</span></span>
- <span data-ttu-id="26db8-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-235">'Razor'</span></span>
- <span data-ttu-id="26db8-236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-236">'SignalR' uid:</span></span> 

<span data-ttu-id="26db8-237">------ | | [Tanımlama bilgisi](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Antiforgery tanımlama bilgilerini oluşturmak için kullanılan ayarları belirler.</span><span class="sxs-lookup"><span data-stu-id="26db8-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="26db8-238">| | [Form alanadı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı.</span><span class="sxs-lookup"><span data-stu-id="26db8-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="26db8-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery sistemi tarafından kullanılan üstbilginin adı.</span><span class="sxs-lookup"><span data-stu-id="26db8-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="26db8-240">Varsa `null` , sistem yalnızca form verilerini dikkate alır.</span><span class="sxs-lookup"><span data-stu-id="26db8-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="26db8-241">| | [Suppressxframeoptionsheader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="26db8-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="26db8-242">Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="26db8-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="26db8-243">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="26db8-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="26db8-244">Seçenek</span><span class="sxs-lookup"><span data-stu-id="26db8-244">Option</span></span> | <span data-ttu-id="26db8-245">Açıklama</span><span class="sxs-lookup"><span data-stu-id="26db8-245">Description</span></span> |
| ---
<span data-ttu-id="26db8-246">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-247">'Blazor'</span></span>
- <span data-ttu-id="26db8-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-248">'Identity'</span></span>
- <span data-ttu-id="26db8-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-250">'Razor'</span></span>
- <span data-ttu-id="26db8-251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-251">'SignalR' uid:</span></span> 

<span data-ttu-id="26db8-252">--- | ---title: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-253">'Blazor'</span></span>
- <span data-ttu-id="26db8-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-254">'Identity'</span></span>
- <span data-ttu-id="26db8-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-256">'Razor'</span></span>
- <span data-ttu-id="26db8-257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="26db8-258">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-259">'Blazor'</span></span>
- <span data-ttu-id="26db8-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-260">'Identity'</span></span>
- <span data-ttu-id="26db8-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-262">'Razor'</span></span>
- <span data-ttu-id="26db8-263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="26db8-264">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="26db8-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="26db8-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26db8-265">'Blazor'</span></span>
- <span data-ttu-id="26db8-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26db8-266">'Identity'</span></span>
- <span data-ttu-id="26db8-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26db8-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="26db8-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26db8-268">'Razor'</span></span>
- <span data-ttu-id="26db8-269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="26db8-269">'SignalR' uid:</span></span> 

<span data-ttu-id="26db8-270">------ | | [Tanımlama bilgisi](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Antiforgery tanımlama bilgilerini oluşturmak için kullanılan ayarları belirler.</span><span class="sxs-lookup"><span data-stu-id="26db8-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="26db8-271">| | [Pişirme ıedomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Tanımlama bilgisinin etki alanı.</span><span class="sxs-lookup"><span data-stu-id="26db8-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="26db8-272">Varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="26db8-272">Defaults to `null`.</span></span> <span data-ttu-id="26db8-273">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="26db8-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="26db8-274">Önerilen alternatif, Cookie. Domain ' dir.</span><span class="sxs-lookup"><span data-stu-id="26db8-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="26db8-275">| | [Tanımlama bilgisi adı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Tanımlama bilgisinin adı.</span><span class="sxs-lookup"><span data-stu-id="26db8-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="26db8-276">Ayarlanmamışsa, sistem [Defaultpişirme ıeprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("ile başlayan benzersiz bir ad oluşturur. AspNetCore. Antiforgery. ").</span><span class="sxs-lookup"><span data-stu-id="26db8-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="26db8-277">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="26db8-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="26db8-278">Önerilen alternatif, Cookie.Name ' dir.</span><span class="sxs-lookup"><span data-stu-id="26db8-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="26db8-279">| | [Tanımlama, ıepath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Tanımlama bilgisinde ayarlanan yol.</span><span class="sxs-lookup"><span data-stu-id="26db8-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="26db8-280">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="26db8-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="26db8-281">Önerilen alternatif, Cookie. Path ' dir.</span><span class="sxs-lookup"><span data-stu-id="26db8-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="26db8-282">| | [Form alanadı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı.</span><span class="sxs-lookup"><span data-stu-id="26db8-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="26db8-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery sistemi tarafından kullanılan üstbilginin adı.</span><span class="sxs-lookup"><span data-stu-id="26db8-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="26db8-284">Varsa `null` , sistem yalnızca form verilerini dikkate alır.</span><span class="sxs-lookup"><span data-stu-id="26db8-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="26db8-285">| | [RequireSSL](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Antiforgery sistemi için HTTPS 'nin gerekli olup olmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="26db8-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="26db8-286">`true`, Https olmayan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="26db8-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="26db8-287">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="26db8-287">Defaults to `false`.</span></span> <span data-ttu-id="26db8-288">Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak.</span><span class="sxs-lookup"><span data-stu-id="26db8-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="26db8-289">Önerilen alternatif, Cookie. SecurePolicy ' i ayarlanmakta.</span><span class="sxs-lookup"><span data-stu-id="26db8-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="26db8-290">| | [Suppressxframeoptionsheader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="26db8-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="26db8-291">Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="26db8-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="26db8-292">Varsayılan olarak olur `false` .</span><span class="sxs-lookup"><span data-stu-id="26db8-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="26db8-293">Daha fazla bilgi için bkz. [tanımlama, ıeauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="26db8-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="26db8-294">Iantiforgery ile antiforgery özelliklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26db8-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="26db8-295">[Iantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) , antiforgery özelliklerini YAPıLANDıRMAK için API sağlar.</span><span class="sxs-lookup"><span data-stu-id="26db8-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="26db8-296">`IAntiforgery`, `Configure` sınıfının yönteminde istenebilir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="26db8-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="26db8-297">Aşağıdaki örnek, bir antiforgery belirteci oluşturmak ve yanıtta bir tanımlama bilgisi olarak (Bu konunun ilerleyen kısımlarında açıklanan varsayılan angular adlandırma kuralını kullanarak) bir uygulama ana sayfasından bir ara yazılım kullanır:</span><span class="sxs-lookup"><span data-stu-id="26db8-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="26db8-298">Antiforgery doğrulaması gerektir</span><span class="sxs-lookup"><span data-stu-id="26db8-298">Require antiforgery validation</span></span>

<span data-ttu-id="26db8-299">[Validateantiforgeritoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) , tek bir eyleme, denetleyiciye veya küresel olarak uygulanabilen bir eylem filtresidir.</span><span class="sxs-lookup"><span data-stu-id="26db8-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="26db8-300">Bu filtre uygulanmış eylemlere yapılan istekler, istek geçerli bir antiforgery belirteci içermiyorsa engellenir.</span><span class="sxs-lookup"><span data-stu-id="26db8-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="26db8-301">`ValidateAntiForgeryToken`Öznitelik, http get istekleri de dahil olmak üzere, işaret eden eylem yöntemlerine istek için bir belirteç gerektirir.</span><span class="sxs-lookup"><span data-stu-id="26db8-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="26db8-302">Öznitelik, `ValidateAntiForgeryToken` uygulamanın denetleyicileri arasında uygulanırsa, özniteliğiyle geçersiz kılınabilir `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="26db8-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="26db8-303">ASP.NET Core, istekleri otomatik olarak almak için antiforgery belirteçleri eklemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="26db8-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="26db8-304">Yalnızca güvenli olmayan HTTP metotları için antiforgery belirteçlerini otomatik olarak doğrula</span><span class="sxs-lookup"><span data-stu-id="26db8-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="26db8-305">ASP.NET Core uygulamalar güvenli HTTP yöntemleri (GET, HEAD, OPTIONS ve TRACE) için antiforgery belirteçleri oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="26db8-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="26db8-306">Özniteliği genel olarak uygulamak `ValidateAntiForgeryToken` ve ardından özniteliklerle geçersiz kılmak yerine `IgnoreAntiforgeryToken` , [oto Validateantiforgerontoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) özniteliği kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="26db8-307">Bu öznitelik, özniteliğiyle aynı şekilde çalışır `ValidateAntiForgeryToken` , ancak AŞAĞıDAKI http yöntemlerini kullanarak yapılan isteklere belirteç gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="26db8-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="26db8-308">GET</span><span class="sxs-lookup"><span data-stu-id="26db8-308">GET</span></span>
* <span data-ttu-id="26db8-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="26db8-309">HEAD</span></span>
* <span data-ttu-id="26db8-310">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="26db8-310">OPTIONS</span></span>
* <span data-ttu-id="26db8-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="26db8-311">TRACE</span></span>

<span data-ttu-id="26db8-312">`AutoValidateAntiforgeryToken`API olmayan senaryolara yönelik olarak kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="26db8-313">Bu, varsayılan olarak POST eylemlerinin korunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="26db8-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="26db8-314">Diğer bir deyişle, `ValidateAntiForgeryToken` tek tek eylem yöntemlerine uygulanmamışsa, varsayılan olarak antiforgery belirteçlerini yok saymanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="26db8-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="26db8-315">Bu senaryoda, bir POST eylemi yönteminin korumasız olarak bırakılması, uygulamanın CSRF saldırılarına karşı savunmasız bırakılması daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="26db8-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="26db8-316">Tüm gönderimler, antiforgery belirtecini göndermelidir.</span><span class="sxs-lookup"><span data-stu-id="26db8-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="26db8-317">API 'lerin, belirtecin tanımlama bilgisi olmayan bölümünü göndermek için otomatik bir mekanizması yoktur.</span><span class="sxs-lookup"><span data-stu-id="26db8-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="26db8-318">Uygulama, büyük olasılıkla istemci kodu uygulamasına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26db8-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="26db8-319">Bazı örnekler aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="26db8-319">Some examples are shown below:</span></span>

<span data-ttu-id="26db8-320">Sınıf düzeyi örnek:</span><span class="sxs-lookup"><span data-stu-id="26db8-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="26db8-321">Genel örnek:</span><span class="sxs-lookup"><span data-stu-id="26db8-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26db8-322">servislere. AddMvc (Options => seçenekleri. Filters. Add (New, oto Validateantiforgeryıtokenattribute ()));</span><span class="sxs-lookup"><span data-stu-id="26db8-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="26db8-323">Küresel veya denetleyici antiforgery özniteliklerini geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="26db8-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="26db8-324">[Ignoreantiforgeri Token](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtresi, belirli bir eyleme (veya denetleyiciye) yönelik bir antiforgery belirtecinin gereksinimini ortadan kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26db8-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="26db8-325">Uygulandığında, bu filtre `ValidateAntiForgeryToken` ve `AutoValidateAntiforgeryToken` daha yüksek düzeyde (genel olarak veya bir denetleyicide) belirtilen filtreleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="26db8-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="26db8-326">Kimlik doğrulamasından sonra belirteçleri Yenile</span><span class="sxs-lookup"><span data-stu-id="26db8-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="26db8-327">Kullanıcının kimliği doğrulandıktan sonra, Kullanıcı bir görünüm veya sayfalar sayfasına yönlendirildikten sonra belirteçlerin yenilenmesi gerekir Razor .</span><span class="sxs-lookup"><span data-stu-id="26db8-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="26db8-328">JavaScript, AJAX ve maça</span><span class="sxs-lookup"><span data-stu-id="26db8-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="26db8-329">Geleneksel HTML tabanlı uygulamalarda, antiforgery belirteçleri gizli form alanları kullanılarak sunucuya geçirilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="26db8-330">Modern JavaScript tabanlı uygulamalar ve maça 'Lar, programlama yoluyla birçok istek yapılır.</span><span class="sxs-lookup"><span data-stu-id="26db8-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="26db8-331">Bu AJAX istekleri, belirteci göndermek için diğer teknikleri (istek üstbilgileri veya tanımlama bilgileri gibi) kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="26db8-332">Kimlik bilgileri kimlik doğrulama belirteçlerini depolamak ve sunucudaki API isteklerinin kimliğini doğrulamak için kullanılıyorsa, CSRF olası bir sorundur.</span><span class="sxs-lookup"><span data-stu-id="26db8-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="26db8-333">Yerel depolama, belirteci depolamak için kullanılıyorsa, yerel depolama alanındaki değerler her istek ile sunucuya otomatik olarak gönderilmediğinden CSRF güvenlik açığı azaltılabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="26db8-334">Bu nedenle, istemci üzerinde antiforgery belirtecini depolamak ve belirteci istek üst bilgisi olarak göndermek için yerel depolama kullanmak önerilen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="26db8-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="26db8-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="26db8-335">JavaScript</span></span>

<span data-ttu-id="26db8-336">Görünümler ile JavaScript kullanarak, belirteç, görünümün içinden bir hizmet kullanılarak oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="26db8-337">[Microsoft. AspNetCore. antiforgery. ıantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) hizmetini görünüme ekleyin ve [Getandstorebelirteçlerini](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)çağırın:</span><span class="sxs-lookup"><span data-stu-id="26db8-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="26db8-338">Bu yaklaşım, sunucudan tanımlama bilgilerini ayarlama veya istemciden okuma konusunda doğrudan anlaşma gereksinimini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="26db8-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="26db8-339">Önceki örnekte, AJAX GÖNDERI üst bilgisinin gizli alan değerini okumak için JavaScript kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="26db8-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="26db8-340">JavaScript, tanımlama bilgilerinde belirteçlere de erişebilir ve belirtecin değerine sahip bir üst bilgi oluşturmak için tanımlama bilgisinin içeriğini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="26db8-341">Çağrılan bir üst bilgide belirteç göndermek için komut dosyası isteklerinin kabul edilmediği varsayılarak `X-CSRF-TOKEN` , bir üst bilgiyi aramak için antiforgery hizmetini yapılandırın `X-CSRF-TOKEN` :</span><span class="sxs-lookup"><span data-stu-id="26db8-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="26db8-342">Aşağıdaki örnek, uygun üst bilgiyle bir AJAX isteği oluşturmak için JavaScript kullanır:</span><span class="sxs-lookup"><span data-stu-id="26db8-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
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

var csrfToken = getCookie("CSRF-TOKEN");

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

### <a name="angularjs"></a><span data-ttu-id="26db8-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="26db8-343">AngularJS</span></span>

<span data-ttu-id="26db8-344">AngularJS, CSRF adresine yönelik bir kural kullanır.</span><span class="sxs-lookup"><span data-stu-id="26db8-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="26db8-345">Sunucu, adla bir tanımlama bilgisi gönderirse `XSRF-TOKEN` , AngularJS `$http` hizmeti, sunucuya istek gönderdiğinde tanımlama bilgisi değerini bir üstbilgiye ekler.</span><span class="sxs-lookup"><span data-stu-id="26db8-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="26db8-346">Bu işlem otomatiktir.</span><span class="sxs-lookup"><span data-stu-id="26db8-346">This process is automatic.</span></span> <span data-ttu-id="26db8-347">Üstbilginin istemcide açıkça ayarlanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="26db8-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="26db8-348">Üst bilgi adı `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="26db8-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="26db8-349">Sunucunun bu üstbilgiyi algılaması ve içeriğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="26db8-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="26db8-350">ASP.NET Core API 'nin uygulama başlangıcında bu kurala göre çalışması için:</span><span class="sxs-lookup"><span data-stu-id="26db8-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="26db8-351">Uygulamanızı adlı bir tanımlama bilgisinde belirteç sunacak şekilde yapılandırın `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="26db8-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="26db8-352">Antiforgery hizmetini adlı üstbilgiyi aramak üzere yapılandırın `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="26db8-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
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

<span data-ttu-id="26db8-353">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26db8-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="26db8-354">Antiforgery 'yi uzat</span><span class="sxs-lookup"><span data-stu-id="26db8-354">Extend antiforgery</span></span>

<span data-ttu-id="26db8-355">[Iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) türü, geliştiricilerin her bir belirteçte ek verileri yuvarlak aşağı getirerek CSRF sisteminin davranışını genişletmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="26db8-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="26db8-356">[Getaddıtionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) yöntemi, bir alan belirtecinin oluşturulduğu her seferinde çağrılır ve döndürülen değer oluşturulan belirtecin içine gömülür.</span><span class="sxs-lookup"><span data-stu-id="26db8-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="26db8-357">Bir uygulayıcısı, bir zaman damgası, bir kerelik anahtar veya başka bir değer döndürebilir ve ardından, belirteç doğrulandığında bu verileri doğrulamak için [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) öğesini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="26db8-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="26db8-358">İstemcinin Kullanıcı adı, oluşturulan belirteçlere zaten eklenmiş olduğundan, bu bilgileri eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="26db8-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="26db8-359">Bir belirteç ek verileri içeriyorsa ancak `IAntiForgeryAdditionalDataProvider` yapılandırılmamışsa, ek veriler doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="26db8-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26db8-360">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="26db8-360">Additional resources</span></span>

* <span data-ttu-id="26db8-361">[Open Web Application Security projesinde](https://www.owasp.org/index.php/Main_Page) [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="26db8-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
