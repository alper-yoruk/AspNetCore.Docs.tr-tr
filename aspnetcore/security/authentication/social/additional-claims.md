---
<span data-ttu-id="42a61-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-102">'Blazor'</span></span>
- <span data-ttu-id="42a61-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-103">'Identity'</span></span>
- <span data-ttu-id="42a61-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-105">'Razor'</span></span>
- <span data-ttu-id="42a61-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="42a61-107">ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme</span><span class="sxs-lookup"><span data-stu-id="42a61-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42a61-108">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="42a61-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="42a61-109">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="42a61-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="42a61-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42a61-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42a61-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="42a61-111">Prerequisites</span></span>

<span data-ttu-id="42a61-112">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="42a61-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="42a61-113">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="42a61-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="42a61-114">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="42a61-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="42a61-115">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="42a61-116">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="42a61-116">Set the client ID and client secret</span></span>

<span data-ttu-id="42a61-117">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="42a61-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="42a61-118">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="42a61-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="42a61-119">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="42a61-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="42a61-120">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="42a61-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="42a61-121">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="42a61-122">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="42a61-123">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="42a61-124">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="42a61-125">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="42a61-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="42a61-126">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="42a61-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="42a61-127">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="42a61-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="42a61-128">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="42a61-128">Establish the authentication scope</span></span>

<span data-ttu-id="42a61-129">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="42a61-130">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="42a61-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="42a61-131">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="42a61-131">Provider</span></span>  | <span data-ttu-id="42a61-132">Kapsam</span><span class="sxs-lookup"><span data-stu-id="42a61-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="42a61-133">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-134">'Blazor'</span></span>
- <span data-ttu-id="42a61-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-135">'Identity'</span></span>
- <span data-ttu-id="42a61-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-137">'Razor'</span></span>
- <span data-ttu-id="42a61-138">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-139">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-140">'Blazor'</span></span>
- <span data-ttu-id="42a61-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-141">'Identity'</span></span>
- <span data-ttu-id="42a61-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-143">'Razor'</span></span>
- <span data-ttu-id="42a61-144">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-144">'SignalR' uid:</span></span> 

<span data-ttu-id="42a61-145">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-146">'Blazor'</span></span>
- <span data-ttu-id="42a61-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-147">'Identity'</span></span>
- <span data-ttu-id="42a61-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-149">'Razor'</span></span>
- <span data-ttu-id="42a61-150">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-151">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-152">'Blazor'</span></span>
- <span data-ttu-id="42a61-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-153">'Identity'</span></span>
- <span data-ttu-id="42a61-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-155">'Razor'</span></span>
- <span data-ttu-id="42a61-156">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-157">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-158">'Blazor'</span></span>
- <span data-ttu-id="42a61-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-159">'Identity'</span></span>
- <span data-ttu-id="42a61-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-161">'Razor'</span></span>
- <span data-ttu-id="42a61-162">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-163">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-164">'Blazor'</span></span>
- <span data-ttu-id="42a61-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-165">'Identity'</span></span>
- <span data-ttu-id="42a61-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-167">'Razor'</span></span>
- <span data-ttu-id="42a61-168">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-169">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-170">'Blazor'</span></span>
- <span data-ttu-id="42a61-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-171">'Identity'</span></span>
- <span data-ttu-id="42a61-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-173">'Razor'</span></span>
- <span data-ttu-id="42a61-174">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-175">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-176">'Blazor'</span></span>
- <span data-ttu-id="42a61-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-177">'Identity'</span></span>
- <span data-ttu-id="42a61-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-179">'Razor'</span></span>
- <span data-ttu-id="42a61-180">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-181">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-182">'Blazor'</span></span>
- <span data-ttu-id="42a61-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-183">'Identity'</span></span>
- <span data-ttu-id="42a61-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-185">'Razor'</span></span>
- <span data-ttu-id="42a61-186">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-187">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-188">'Blazor'</span></span>
- <span data-ttu-id="42a61-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-189">'Identity'</span></span>
- <span data-ttu-id="42a61-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-191">'Razor'</span></span>
- <span data-ttu-id="42a61-192">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-193">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-194">'Blazor'</span></span>
- <span data-ttu-id="42a61-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-195">'Identity'</span></span>
- <span data-ttu-id="42a61-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-197">'Razor'</span></span>
- <span data-ttu-id="42a61-198">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-199">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-200">'Blazor'</span></span>
- <span data-ttu-id="42a61-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-201">'Identity'</span></span>
- <span data-ttu-id="42a61-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-203">'Razor'</span></span>
- <span data-ttu-id="42a61-204">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-205">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-206">'Blazor'</span></span>
- <span data-ttu-id="42a61-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-207">'Identity'</span></span>
- <span data-ttu-id="42a61-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-209">'Razor'</span></span>
- <span data-ttu-id="42a61-210">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-211">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-212">'Blazor'</span></span>
- <span data-ttu-id="42a61-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-213">'Identity'</span></span>
- <span data-ttu-id="42a61-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-215">'Razor'</span></span>
- <span data-ttu-id="42a61-216">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-217">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-218">'Blazor'</span></span>
- <span data-ttu-id="42a61-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-219">'Identity'</span></span>
- <span data-ttu-id="42a61-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-221">'Razor'</span></span>
- <span data-ttu-id="42a61-222">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-223">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-224">'Blazor'</span></span>
- <span data-ttu-id="42a61-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-225">'Identity'</span></span>
- <span data-ttu-id="42a61-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-227">'Razor'</span></span>
- <span data-ttu-id="42a61-228">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-229">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-230">'Blazor'</span></span>
- <span data-ttu-id="42a61-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-231">'Identity'</span></span>
- <span data-ttu-id="42a61-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-233">'Razor'</span></span>
- <span data-ttu-id="42a61-234">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-235">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-236">'Blazor'</span></span>
- <span data-ttu-id="42a61-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-237">'Identity'</span></span>
- <span data-ttu-id="42a61-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-239">'Razor'</span></span>
- <span data-ttu-id="42a61-240">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-241">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-242">'Blazor'</span></span>
- <span data-ttu-id="42a61-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-243">'Identity'</span></span>
- <span data-ttu-id="42a61-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-245">'Razor'</span></span>
- <span data-ttu-id="42a61-246">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-247">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-248">'Blazor'</span></span>
- <span data-ttu-id="42a61-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-249">'Identity'</span></span>
- <span data-ttu-id="42a61-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-251">'Razor'</span></span>
- <span data-ttu-id="42a61-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-253">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-254">'Blazor'</span></span>
- <span data-ttu-id="42a61-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-255">'Identity'</span></span>
- <span data-ttu-id="42a61-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-257">'Razor'</span></span>
- <span data-ttu-id="42a61-258">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-259">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-260">'Blazor'</span></span>
- <span data-ttu-id="42a61-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-261">'Identity'</span></span>
- <span data-ttu-id="42a61-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-263">'Razor'</span></span>
- <span data-ttu-id="42a61-264">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-265">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-266">'Blazor'</span></span>
- <span data-ttu-id="42a61-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-267">'Identity'</span></span>
- <span data-ttu-id="42a61-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-269">'Razor'</span></span>
- <span data-ttu-id="42a61-270">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-271">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-272">'Blazor'</span></span>
- <span data-ttu-id="42a61-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-273">'Identity'</span></span>
- <span data-ttu-id="42a61-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-275">'Razor'</span></span>
- <span data-ttu-id="42a61-276">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-277">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-278">'Blazor'</span></span>
- <span data-ttu-id="42a61-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-279">'Identity'</span></span>
- <span data-ttu-id="42a61-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-281">'Razor'</span></span>
- <span data-ttu-id="42a61-282">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-283">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-284">'Blazor'</span></span>
- <span data-ttu-id="42a61-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-285">'Identity'</span></span>
- <span data-ttu-id="42a61-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-287">'Razor'</span></span>
- <span data-ttu-id="42a61-288">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-289">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-290">'Blazor'</span></span>
- <span data-ttu-id="42a61-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-291">'Identity'</span></span>
- <span data-ttu-id="42a61-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-293">'Razor'</span></span>
- <span data-ttu-id="42a61-294">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-295">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-296">'Blazor'</span></span>
- <span data-ttu-id="42a61-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-297">'Identity'</span></span>
- <span data-ttu-id="42a61-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-299">'Razor'</span></span>
- <span data-ttu-id="42a61-300">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-301">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-302">'Blazor'</span></span>
- <span data-ttu-id="42a61-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-303">'Identity'</span></span>
- <span data-ttu-id="42a61-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-305">'Razor'</span></span>
- <span data-ttu-id="42a61-306">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-307">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-308">'Blazor'</span></span>
- <span data-ttu-id="42a61-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-309">'Identity'</span></span>
- <span data-ttu-id="42a61-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-311">'Razor'</span></span>
- <span data-ttu-id="42a61-312">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-313">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-314">'Blazor'</span></span>
- <span data-ttu-id="42a61-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-315">'Identity'</span></span>
- <span data-ttu-id="42a61-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-317">'Razor'</span></span>
- <span data-ttu-id="42a61-318">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-319">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-320">'Blazor'</span></span>
- <span data-ttu-id="42a61-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-321">'Identity'</span></span>
- <span data-ttu-id="42a61-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-323">'Razor'</span></span>
- <span data-ttu-id="42a61-324">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-324">'SignalR' uid:</span></span> 

<span data-ttu-id="42a61-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="42a61-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="42a61-326">Örnek uygulamada, Google 'ın `userinfo.profile` kapsamı <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> , üzerinde çağrıldığında Framework tarafından otomatik olarak eklenir <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42a61-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="42a61-327">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42a61-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="42a61-328">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="42a61-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="42a61-329">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="42a61-329">Map user data keys and create claims</span></span>

<span data-ttu-id="42a61-330">Sağlayıcının seçeneklerinde, <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="42a61-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="42a61-331">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes> ..</span><span class="sxs-lookup"><span data-stu-id="42a61-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="42a61-332">Örnek uygulama `urn:google:locale` `urn:google:picture` , `locale` Google Kullanıcı verilerinde ve anahtarlarından yerel ayar () ve resim () talepleri oluşturur `picture` :</span><span class="sxs-lookup"><span data-stu-id="42a61-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="42a61-333">İçinde `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ), ile uygulamasında oturum açtı <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="42a61-334">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` dan kullanılabilir Kullanıcı verileri için bir talep saklayabilir <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="42a61-335">Örnek uygulamada `OnPostConfirmationAsync` (*Account/externallogin. cshtml. cs*), `urn:google:locale` `urn:google:picture` `ApplicationUser` için bir talep dahil olmak üzere, oturum açan için yerel ayar () ve resim () taleplerini belirler <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="42a61-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="42a61-336">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="42a61-337">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="42a61-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="42a61-338">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="42a61-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="42a61-339">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="42a61-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="42a61-340">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="42a61-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="42a61-341">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="42a61-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="42a61-342"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>Kimlik bilgisi kimlik doğrulaması ara yazılımı için özel ' i kullanarak <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> kimlik istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="42a61-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="42a61-343">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="42a61-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="42a61-344">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="42a61-344">Save the access token</span></span>

<span data-ttu-id="42a61-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="42a61-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="42a61-346">`SaveTokens`, `false` son kimlik doğrulama tanımlama bilgisinin boyutunu azaltmak için varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="42a61-347">Örnek uygulama, değerini içinde olarak ayarlar `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="42a61-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="42a61-348">`OnPostConfirmationAsync`Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)), içindeki dış sağlayıcıdan depolayın `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="42a61-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="42a61-349">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs*içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="42a61-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="42a61-350">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="42a61-350">How to add additional custom tokens</span></span>

<span data-ttu-id="42a61-351">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için `SaveTokens` , örnek uygulama bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name için geçerli bir ile ekler <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="42a61-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="42a61-352">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="42a61-352">Creating and adding claims</span></span>

<span data-ttu-id="42a61-353">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="42a61-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="42a61-354">Daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> . ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="42a61-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="42a61-355">Kullanıcılar <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> , Özet yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="42a61-356">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="42a61-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="42a61-357">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="42a61-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="42a61-358">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen için tüm talep eylemlerini kaldırır <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="42a61-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="42a61-359">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen bir talebini siler <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="42a61-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="42a61-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42a61-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="42a61-361">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="42a61-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42a61-362">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="42a61-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="42a61-363">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="42a61-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="42a61-364">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42a61-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42a61-365">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="42a61-365">Prerequisites</span></span>

<span data-ttu-id="42a61-366">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="42a61-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="42a61-367">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="42a61-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="42a61-368">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="42a61-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="42a61-369">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="42a61-370">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="42a61-370">Set the client ID and client secret</span></span>

<span data-ttu-id="42a61-371">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="42a61-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="42a61-372">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="42a61-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="42a61-373">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="42a61-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="42a61-374">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="42a61-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="42a61-375">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="42a61-376">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="42a61-377">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="42a61-378">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="42a61-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="42a61-379">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="42a61-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="42a61-380">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="42a61-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="42a61-381">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="42a61-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="42a61-382">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="42a61-382">Establish the authentication scope</span></span>

<span data-ttu-id="42a61-383">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="42a61-384">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="42a61-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="42a61-385">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="42a61-385">Provider</span></span>  | <span data-ttu-id="42a61-386">Kapsam</span><span class="sxs-lookup"><span data-stu-id="42a61-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="42a61-387">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-388">'Blazor'</span></span>
- <span data-ttu-id="42a61-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-389">'Identity'</span></span>
- <span data-ttu-id="42a61-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-391">'Razor'</span></span>
- <span data-ttu-id="42a61-392">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-393">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-394">'Blazor'</span></span>
- <span data-ttu-id="42a61-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-395">'Identity'</span></span>
- <span data-ttu-id="42a61-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-397">'Razor'</span></span>
- <span data-ttu-id="42a61-398">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-398">'SignalR' uid:</span></span> 

<span data-ttu-id="42a61-399">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-400">'Blazor'</span></span>
- <span data-ttu-id="42a61-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-401">'Identity'</span></span>
- <span data-ttu-id="42a61-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-403">'Razor'</span></span>
- <span data-ttu-id="42a61-404">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-405">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-406">'Blazor'</span></span>
- <span data-ttu-id="42a61-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-407">'Identity'</span></span>
- <span data-ttu-id="42a61-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-409">'Razor'</span></span>
- <span data-ttu-id="42a61-410">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-411">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-412">'Blazor'</span></span>
- <span data-ttu-id="42a61-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-413">'Identity'</span></span>
- <span data-ttu-id="42a61-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-415">'Razor'</span></span>
- <span data-ttu-id="42a61-416">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-417">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-418">'Blazor'</span></span>
- <span data-ttu-id="42a61-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-419">'Identity'</span></span>
- <span data-ttu-id="42a61-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-421">'Razor'</span></span>
- <span data-ttu-id="42a61-422">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-423">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-424">'Blazor'</span></span>
- <span data-ttu-id="42a61-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-425">'Identity'</span></span>
- <span data-ttu-id="42a61-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-427">'Razor'</span></span>
- <span data-ttu-id="42a61-428">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-429">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-430">'Blazor'</span></span>
- <span data-ttu-id="42a61-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-431">'Identity'</span></span>
- <span data-ttu-id="42a61-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-433">'Razor'</span></span>
- <span data-ttu-id="42a61-434">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-435">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-436">'Blazor'</span></span>
- <span data-ttu-id="42a61-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-437">'Identity'</span></span>
- <span data-ttu-id="42a61-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-439">'Razor'</span></span>
- <span data-ttu-id="42a61-440">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-441">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-442">'Blazor'</span></span>
- <span data-ttu-id="42a61-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-443">'Identity'</span></span>
- <span data-ttu-id="42a61-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-445">'Razor'</span></span>
- <span data-ttu-id="42a61-446">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-447">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-448">'Blazor'</span></span>
- <span data-ttu-id="42a61-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-449">'Identity'</span></span>
- <span data-ttu-id="42a61-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-451">'Razor'</span></span>
- <span data-ttu-id="42a61-452">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-453">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-454">'Blazor'</span></span>
- <span data-ttu-id="42a61-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-455">'Identity'</span></span>
- <span data-ttu-id="42a61-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-457">'Razor'</span></span>
- <span data-ttu-id="42a61-458">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-459">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-460">'Blazor'</span></span>
- <span data-ttu-id="42a61-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-461">'Identity'</span></span>
- <span data-ttu-id="42a61-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-463">'Razor'</span></span>
- <span data-ttu-id="42a61-464">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-465">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-466">'Blazor'</span></span>
- <span data-ttu-id="42a61-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-467">'Identity'</span></span>
- <span data-ttu-id="42a61-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-469">'Razor'</span></span>
- <span data-ttu-id="42a61-470">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-471">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-472">'Blazor'</span></span>
- <span data-ttu-id="42a61-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-473">'Identity'</span></span>
- <span data-ttu-id="42a61-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-475">'Razor'</span></span>
- <span data-ttu-id="42a61-476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-477">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-478">'Blazor'</span></span>
- <span data-ttu-id="42a61-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-479">'Identity'</span></span>
- <span data-ttu-id="42a61-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-481">'Razor'</span></span>
- <span data-ttu-id="42a61-482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-483">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-484">'Blazor'</span></span>
- <span data-ttu-id="42a61-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-485">'Identity'</span></span>
- <span data-ttu-id="42a61-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-487">'Razor'</span></span>
- <span data-ttu-id="42a61-488">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-489">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-490">'Blazor'</span></span>
- <span data-ttu-id="42a61-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-491">'Identity'</span></span>
- <span data-ttu-id="42a61-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-493">'Razor'</span></span>
- <span data-ttu-id="42a61-494">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-495">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-496">'Blazor'</span></span>
- <span data-ttu-id="42a61-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-497">'Identity'</span></span>
- <span data-ttu-id="42a61-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-499">'Razor'</span></span>
- <span data-ttu-id="42a61-500">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-501">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-502">'Blazor'</span></span>
- <span data-ttu-id="42a61-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-503">'Identity'</span></span>
- <span data-ttu-id="42a61-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-505">'Razor'</span></span>
- <span data-ttu-id="42a61-506">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-507">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-508">'Blazor'</span></span>
- <span data-ttu-id="42a61-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-509">'Identity'</span></span>
- <span data-ttu-id="42a61-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-511">'Razor'</span></span>
- <span data-ttu-id="42a61-512">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-513">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-514">'Blazor'</span></span>
- <span data-ttu-id="42a61-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-515">'Identity'</span></span>
- <span data-ttu-id="42a61-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-517">'Razor'</span></span>
- <span data-ttu-id="42a61-518">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-519">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-520">'Blazor'</span></span>
- <span data-ttu-id="42a61-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-521">'Identity'</span></span>
- <span data-ttu-id="42a61-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-523">'Razor'</span></span>
- <span data-ttu-id="42a61-524">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-525">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-526">'Blazor'</span></span>
- <span data-ttu-id="42a61-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-527">'Identity'</span></span>
- <span data-ttu-id="42a61-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-529">'Razor'</span></span>
- <span data-ttu-id="42a61-530">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-531">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-532">'Blazor'</span></span>
- <span data-ttu-id="42a61-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-533">'Identity'</span></span>
- <span data-ttu-id="42a61-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-535">'Razor'</span></span>
- <span data-ttu-id="42a61-536">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-537">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-538">'Blazor'</span></span>
- <span data-ttu-id="42a61-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-539">'Identity'</span></span>
- <span data-ttu-id="42a61-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-541">'Razor'</span></span>
- <span data-ttu-id="42a61-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-543">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-544">'Blazor'</span></span>
- <span data-ttu-id="42a61-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-545">'Identity'</span></span>
- <span data-ttu-id="42a61-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-547">'Razor'</span></span>
- <span data-ttu-id="42a61-548">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-549">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-550">'Blazor'</span></span>
- <span data-ttu-id="42a61-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-551">'Identity'</span></span>
- <span data-ttu-id="42a61-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-553">'Razor'</span></span>
- <span data-ttu-id="42a61-554">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-555">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-556">'Blazor'</span></span>
- <span data-ttu-id="42a61-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-557">'Identity'</span></span>
- <span data-ttu-id="42a61-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-559">'Razor'</span></span>
- <span data-ttu-id="42a61-560">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-561">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-562">'Blazor'</span></span>
- <span data-ttu-id="42a61-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-563">'Identity'</span></span>
- <span data-ttu-id="42a61-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-565">'Razor'</span></span>
- <span data-ttu-id="42a61-566">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-567">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-568">'Blazor'</span></span>
- <span data-ttu-id="42a61-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-569">'Identity'</span></span>
- <span data-ttu-id="42a61-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-571">'Razor'</span></span>
- <span data-ttu-id="42a61-572">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42a61-573">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="42a61-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42a61-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42a61-574">'Blazor'</span></span>
- <span data-ttu-id="42a61-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42a61-575">'Identity'</span></span>
- <span data-ttu-id="42a61-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42a61-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="42a61-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42a61-577">'Razor'</span></span>
- <span data-ttu-id="42a61-578">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="42a61-578">'SignalR' uid:</span></span> 

<span data-ttu-id="42a61-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="42a61-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="42a61-580">Örnek uygulamada, Google 'ın `userinfo.profile` kapsamı <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> , üzerinde çağrıldığında Framework tarafından otomatik olarak eklenir <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42a61-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="42a61-581">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="42a61-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="42a61-582">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="42a61-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="42a61-583">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="42a61-583">Map user data keys and create claims</span></span>

<span data-ttu-id="42a61-584">Sağlayıcının seçeneklerinde, <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="42a61-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="42a61-585">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes> ..</span><span class="sxs-lookup"><span data-stu-id="42a61-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="42a61-586">Örnek uygulama `urn:google:locale` `urn:google:picture` , `locale` Google Kullanıcı verilerinde ve anahtarlarından yerel ayar () ve resim () talepleri oluşturur `picture` :</span><span class="sxs-lookup"><span data-stu-id="42a61-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="42a61-587">İçinde `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ), ile uygulamasında oturum açtı <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="42a61-588">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` dan kullanılabilir Kullanıcı verileri için bir talep saklayabilir <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="42a61-589">Örnek uygulamada `OnPostConfirmationAsync` (*Account/externallogin. cshtml. cs*), `urn:google:locale` `urn:google:picture` `ApplicationUser` için bir talep dahil olmak üzere, oturum açan için yerel ayar () ve resim () taleplerini belirler <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="42a61-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="42a61-590">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="42a61-591">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="42a61-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="42a61-592">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="42a61-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="42a61-593">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="42a61-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="42a61-594">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="42a61-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="42a61-595">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="42a61-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="42a61-596"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore>Kimlik bilgisi kimlik doğrulaması ara yazılımı için özel ' i kullanarak <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> kimlik istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="42a61-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="42a61-597">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="42a61-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="42a61-598">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="42a61-598">Save the access token</span></span>

<span data-ttu-id="42a61-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="42a61-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="42a61-600">`SaveTokens`, `false` son kimlik doğrulama tanımlama bilgisinin boyutunu azaltmak için varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="42a61-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="42a61-601">Örnek uygulama, değerini içinde olarak ayarlar `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="42a61-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="42a61-602">`OnPostConfirmationAsync`Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)), içindeki dış sağlayıcıdan depolayın `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="42a61-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="42a61-603">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs*içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="42a61-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="42a61-604">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="42a61-604">How to add additional custom tokens</span></span>

<span data-ttu-id="42a61-605">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için `SaveTokens` , örnek uygulama bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name için geçerli bir ile ekler <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="42a61-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="42a61-606">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="42a61-606">Creating and adding claims</span></span>

<span data-ttu-id="42a61-607">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="42a61-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="42a61-608">Daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> . ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="42a61-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="42a61-609">Kullanıcılar <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> , Özet yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="42a61-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="42a61-610">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="42a61-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="42a61-611">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="42a61-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="42a61-612">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen için tüm talep eylemlerini kaldırır <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="42a61-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="42a61-613">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen bir talebini siler <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="42a61-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="42a61-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42a61-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="42a61-615">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="42a61-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="42a61-616">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="42a61-616">Additional resources</span></span>

* <span data-ttu-id="42a61-617">[DotNet/aspnetcore mühendislik SocialSample uygulaması](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): bağlantılı örnek uygulama [DotNet/aspnetcore GitHub deposunun](https://github.com/dotnet/AspNetCore) `master` mühendislik dalında bulunur.</span><span class="sxs-lookup"><span data-stu-id="42a61-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="42a61-618">`master`Dal, ASP.NET Core sonraki sürümü için etkin geliştirme altında kod içerir.</span><span class="sxs-lookup"><span data-stu-id="42a61-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="42a61-619">Yayınlanmış bir ASP.NET Core sürümü için örnek uygulamanın bir sürümünü görmek için, **dal** açılan listesini kullanarak bir yayın dalı seçin (örneğin `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="42a61-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
