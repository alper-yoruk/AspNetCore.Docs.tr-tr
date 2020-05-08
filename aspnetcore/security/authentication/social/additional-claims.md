---
title: ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme
author: rick-anderson
description: Dış sağlayıcılardan ek talepler ve belirteçler oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769036"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f1a6e-103">ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme</span><span class="sxs-lookup"><span data-stu-id="f1a6e-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1a6e-104">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f1a6e-105">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f1a6e-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1a6e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1a6e-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f1a6e-107">Prerequisites</span></span>

<span data-ttu-id="f1a6e-108">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f1a6e-109">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f1a6e-110">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f1a6e-111">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f1a6e-112">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="f1a6e-112">Set the client ID and client secret</span></span>

<span data-ttu-id="f1a6e-113">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f1a6e-114">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f1a6e-115">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f1a6e-116">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f1a6e-117">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f1a6e-118">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f1a6e-119">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f1a6e-120">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f1a6e-121">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f1a6e-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f1a6e-122">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="f1a6e-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f1a6e-123">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f1a6e-124">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1a6e-124">Establish the authentication scope</span></span>

<span data-ttu-id="f1a6e-125">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f1a6e-126">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f1a6e-127">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f1a6e-127">Provider</span></span>  | <span data-ttu-id="f1a6e-128">Kapsam</span><span class="sxs-lookup"><span data-stu-id="f1a6e-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f1a6e-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="f1a6e-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f1a6e-130">Google</span><span class="sxs-lookup"><span data-stu-id="f1a6e-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f1a6e-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f1a6e-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f1a6e-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="f1a6e-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f1a6e-133">Örnek uygulamada, Google 'ın `userinfo.profile` kapsamı, <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>üzerinde çağrıldığında Framework tarafından otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f1a6e-134">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f1a6e-135">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` Scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f1a6e-136">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1a6e-136">Map user data keys and create claims</span></span>

<span data-ttu-id="f1a6e-137">Sağlayıcının seçeneklerinde, oturum açma sırasında okunacak uygulama <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> kimliği <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f1a6e-138">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes>..</span><span class="sxs-lookup"><span data-stu-id="f1a6e-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f1a6e-139">Örnek uygulama,`urn:google:locale` `locale` Google Kullanıcı verilerinde ve `picture` anahtarlarından yerel ayar`urn:google:picture`() ve resim () talepleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f1a6e-140">İçinde `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`), ile <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>uygulamasında oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f1a6e-141">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.Identity.UserManager%601> dan kullanılabilir Kullanıcı verileri `ApplicationUser` için bir talep saklayabilir <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f1a6e-142">Örnek `OnPostConfirmationAsync` uygulamada (*Account/externallogin. cshtml. cs*), için <xref:System.Security.Claims.ClaimTypes.GivenName>bir talep dahil olmak üzere`urn:google:locale`, oturum açan `ApplicationUser`için`urn:google:picture`yerel ayar () ve resim () taleplerini belirler:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f1a6e-143">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f1a6e-144">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f1a6e-145">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f1a6e-146">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="f1a6e-147">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f1a6e-148">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f1a6e-149">Kimlik bilgisi kimlik <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> doğrulaması ara yazılımı <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> için özel ' i kullanarak kimlik istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f1a6e-150">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f1a6e-151">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="f1a6e-151">Save the access token</span></span>

<span data-ttu-id="f1a6e-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>başarılı bir yetkilendirmeden <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f1a6e-153">`SaveTokens`, son kimlik `false` doğrulama tanımlama bilgisinin boyutunu azaltmak için varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f1a6e-154">Örnek uygulama, değerini `SaveTokens` `true` içinde <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f1a6e-155">Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)), `ApplicationUser`içindeki dış sağlayıcıdan depolayın `AuthenticationProperties` `OnPostConfirmationAsync`</span><span class="sxs-lookup"><span data-stu-id="f1a6e-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f1a6e-156">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs*içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f1a6e-157">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="f1a6e-157">How to add additional custom tokens</span></span>

<span data-ttu-id="f1a6e-158">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini `SaveTokens`göstermek için, örnek uygulama bir [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) için `TicketCreated`geçerli <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> bir ile ekler:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f1a6e-159">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="f1a6e-159">Creating and adding claims</span></span>

<span data-ttu-id="f1a6e-160">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f1a6e-161">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f1a6e-162">Kullanıcılar, Özet <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f1a6e-163">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f1a6e-164">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="f1a6e-165">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> için tüm talep eylemlerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f1a6e-166">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> bir talebini siler.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f1a6e-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f1a6e-168">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="f1a6e-168">Sample app output</span></span>

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

<span data-ttu-id="f1a6e-169">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f1a6e-170">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f1a6e-171">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1a6e-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1a6e-172">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f1a6e-172">Prerequisites</span></span>

<span data-ttu-id="f1a6e-173">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f1a6e-174">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f1a6e-175">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f1a6e-176">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f1a6e-177">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="f1a6e-177">Set the client ID and client secret</span></span>

<span data-ttu-id="f1a6e-178">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f1a6e-179">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f1a6e-180">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f1a6e-181">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f1a6e-182">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f1a6e-183">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f1a6e-184">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f1a6e-185">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f1a6e-186">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f1a6e-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f1a6e-187">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="f1a6e-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f1a6e-188">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f1a6e-189">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1a6e-189">Establish the authentication scope</span></span>

<span data-ttu-id="f1a6e-190">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f1a6e-191">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f1a6e-192">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f1a6e-192">Provider</span></span>  | <span data-ttu-id="f1a6e-193">Kapsam</span><span class="sxs-lookup"><span data-stu-id="f1a6e-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f1a6e-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="f1a6e-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f1a6e-195">Google</span><span class="sxs-lookup"><span data-stu-id="f1a6e-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f1a6e-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f1a6e-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f1a6e-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="f1a6e-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f1a6e-198">Örnek uygulamada, Google 'ın `userinfo.profile` kapsamı, <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>üzerinde çağrıldığında Framework tarafından otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f1a6e-199">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f1a6e-200">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` Scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f1a6e-201">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1a6e-201">Map user data keys and create claims</span></span>

<span data-ttu-id="f1a6e-202">Sağlayıcının seçeneklerinde, oturum açma sırasında okunacak uygulama <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> kimliği <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f1a6e-203">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes>..</span><span class="sxs-lookup"><span data-stu-id="f1a6e-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f1a6e-204">Örnek uygulama,`urn:google:locale` `locale` Google Kullanıcı verilerinde ve `picture` anahtarlarından yerel ayar`urn:google:picture`() ve resim () talepleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f1a6e-205">İçinde `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`), ile <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>uygulamasında oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f1a6e-206">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.Identity.UserManager%601> dan kullanılabilir Kullanıcı verileri `ApplicationUser` için bir talep saklayabilir <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f1a6e-207">Örnek `OnPostConfirmationAsync` uygulamada (*Account/externallogin. cshtml. cs*), için <xref:System.Security.Claims.ClaimTypes.GivenName>bir talep dahil olmak üzere`urn:google:locale`, oturum açan `ApplicationUser`için`urn:google:picture`yerel ayar () ve resim () taleplerini belirler:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f1a6e-208">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f1a6e-209">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f1a6e-210">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f1a6e-211">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="f1a6e-212">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f1a6e-213">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f1a6e-214">Kimlik bilgisi kimlik <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> doğrulaması ara yazılımı <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> için özel ' i kullanarak kimlik istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f1a6e-215">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f1a6e-216">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="f1a6e-216">Save the access token</span></span>

<span data-ttu-id="f1a6e-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>başarılı bir yetkilendirmeden <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f1a6e-218">`SaveTokens`, son kimlik `false` doğrulama tanımlama bilgisinin boyutunu azaltmak için varsayılan olarak olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f1a6e-219">Örnek uygulama, değerini `SaveTokens` `true` içinde <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f1a6e-220">Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)), `ApplicationUser`içindeki dış sağlayıcıdan depolayın `AuthenticationProperties` `OnPostConfirmationAsync`</span><span class="sxs-lookup"><span data-stu-id="f1a6e-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f1a6e-221">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs*içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f1a6e-222">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="f1a6e-222">How to add additional custom tokens</span></span>

<span data-ttu-id="f1a6e-223">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini `SaveTokens`göstermek için, örnek uygulama bir [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) için `TicketCreated`geçerli <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> bir ile ekler:</span><span class="sxs-lookup"><span data-stu-id="f1a6e-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f1a6e-224">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="f1a6e-224">Creating and adding claims</span></span>

<span data-ttu-id="f1a6e-225">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f1a6e-226">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f1a6e-227">Kullanıcılar, Özet <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f1a6e-228">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f1a6e-229">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="f1a6e-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="f1a6e-230">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> için tüm talep eylemlerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f1a6e-231">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> bir talebini siler.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f1a6e-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f1a6e-233">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="f1a6e-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f1a6e-234">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f1a6e-234">Additional resources</span></span>

* <span data-ttu-id="f1a6e-235">[DotNet/aspnetcore mühendislik socialsample uygulaması](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; bağlantılı örnek uygulama [DotNet/aspnetcore GitHub deposunun](https://github.com/dotnet/AspNetCore) `master` mühendislik dalında.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f1a6e-236">`master` Dal, ASP.NET Core sonraki sürümü için etkin geliştirme altında kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f1a6e-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f1a6e-237">Yayınlanmış bir ASP.NET Core sürümü için örnek uygulamanın bir sürümünü görmek için, **dal** açılan listesini kullanarak bir yayın dalı seçin (örneğin `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="f1a6e-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
