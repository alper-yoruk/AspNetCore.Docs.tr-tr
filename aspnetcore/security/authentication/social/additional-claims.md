---
title: ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme
author: rick-anderson
description: Dış sağlayıcılardan ek talepler ve belirteçler oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 4503291ff887f79b1ad6eacd4e56943ce23335bc
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141514"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="7b215-103">ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme</span><span class="sxs-lookup"><span data-stu-id="7b215-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b215-104">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="7b215-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7b215-105">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="7b215-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7b215-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b215-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b215-107">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7b215-107">Prerequisites</span></span>

<span data-ttu-id="7b215-108">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="7b215-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7b215-109">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="7b215-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7b215-110">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7b215-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7b215-111">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="7b215-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7b215-112">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="7b215-112">Set the client ID and client secret</span></span>

<span data-ttu-id="7b215-113">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="7b215-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7b215-114">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b215-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7b215-115">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7b215-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7b215-116">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="7b215-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7b215-117">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7b215-118">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7b215-119">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7b215-120">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7b215-121">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="7b215-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7b215-122">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="7b215-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7b215-123">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="7b215-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7b215-124">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="7b215-124">Establish the authentication scope</span></span>

<span data-ttu-id="7b215-125">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7b215-126">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7b215-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7b215-127">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="7b215-127">Provider</span></span>  | <span data-ttu-id="7b215-128">Kapsam</span><span class="sxs-lookup"><span data-stu-id="7b215-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7b215-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="7b215-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7b215-130">Google</span><span class="sxs-lookup"><span data-stu-id="7b215-130">Google</span></span>    | <span data-ttu-id="7b215-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="7b215-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="7b215-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7b215-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7b215-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="7b215-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7b215-134">Örnek uygulamada, Google 'ın `profile` , `email` ve kapsamları ' de `openid` çağrıldığında Framework tarafından otomatik olarak eklenir <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="7b215-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7b215-135">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7b215-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7b215-136">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7b215-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7b215-137">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="7b215-137">Map user data keys and create claims</span></span>

<span data-ttu-id="7b215-138">Sağlayıcının seçeneklerinde, <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="7b215-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7b215-139">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes> ..</span><span class="sxs-lookup"><span data-stu-id="7b215-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7b215-140">Örnek uygulama `urn:google:locale` `urn:google:picture` , `locale` Google Kullanıcı verilerinde ve anahtarlarından yerel ayar () ve resim () talepleri oluşturur `picture` :</span><span class="sxs-lookup"><span data-stu-id="7b215-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7b215-141">İçinde `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , bir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ), ile uygulamasında oturum açtı <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-141">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7b215-142">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> `ApplicationUser` dan kullanılabilir Kullanıcı verileri için bir talep saklayabilir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-142">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7b215-143">Örnek uygulamada `OnPostConfirmationAsync` ( *Account/externallogin. cshtml. cs* ), `urn:google:locale` `urn:google:picture` `ApplicationUser` için bir talep dahil olmak üzere, oturum açan için yerel ayar () ve resim () taleplerini belirler <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="7b215-143">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7b215-144">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulamasında depolanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="7b215-144">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="7b215-145">Kimlik doğrulaması :::no-loc(cookie)::: çok büyükse uygulamanın başarısız olmasına neden olabilir çünkü:</span><span class="sxs-lookup"><span data-stu-id="7b215-145">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7b215-146">Tarayıcı :::no-loc(cookie)::: üstbilginin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="7b215-146">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="7b215-147">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="7b215-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="7b215-148">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="7b215-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7b215-149">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="7b215-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7b215-150"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore>Kimlik doğrulama ara yazılımı için özel ' i kullanarak kimlik :::no-loc(Cookie)::: <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="7b215-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7b215-151">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="7b215-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7b215-152">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="7b215-152">Save the access token</span></span>

<span data-ttu-id="7b215-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="7b215-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7b215-154">`SaveTokens` , `false` son kimlik doğrulamanın boyutunu azaltmak için varsayılan olarak olarak ayarlanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="7b215-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="7b215-155">Örnek uygulama, değerini içinde olarak ayarlar `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="7b215-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7b215-156">`OnPostConfirmationAsync`Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. authenticationtokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)), içindeki dış sağlayıcıdan depolayın `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="7b215-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7b215-157">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs* içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="7b215-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7b215-158">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="7b215-158">How to add additional custom tokens</span></span>

<span data-ttu-id="7b215-159">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için `SaveTokens` , örnek uygulama bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name için geçerli bir ile ekler <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="7b215-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7b215-160">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="7b215-160">Creating and adding claims</span></span>

<span data-ttu-id="7b215-161">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b215-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7b215-162">Daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> . ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="7b215-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7b215-163">Kullanıcılar <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> , Özet yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7b215-164">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7b215-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7b215-165">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="7b215-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="7b215-166">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen için tüm talep eylemlerini kaldırır <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="7b215-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7b215-167">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen bir talebini siler <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="7b215-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7b215-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> , protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7b215-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7b215-169">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="7b215-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

<span data-ttu-id="7b215-170">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="7b215-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7b215-171">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="7b215-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7b215-172">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b215-172">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b215-173">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7b215-173">Prerequisites</span></span>

<span data-ttu-id="7b215-174">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="7b215-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7b215-175">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="7b215-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7b215-176">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7b215-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7b215-177">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="7b215-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7b215-178">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="7b215-178">Set the client ID and client secret</span></span>

<span data-ttu-id="7b215-179">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="7b215-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7b215-180">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b215-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7b215-181">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7b215-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7b215-182">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="7b215-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7b215-183">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7b215-184">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7b215-185">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7b215-186">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7b215-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7b215-187">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="7b215-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7b215-188">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="7b215-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7b215-189">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="7b215-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7b215-190">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="7b215-190">Establish the authentication scope</span></span>

<span data-ttu-id="7b215-191">' İ belirterek sağlayıcıdan alma izinlerinin listesini belirtin <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7b215-192">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7b215-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7b215-193">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="7b215-193">Provider</span></span>  | <span data-ttu-id="7b215-194">Kapsam</span><span class="sxs-lookup"><span data-stu-id="7b215-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7b215-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="7b215-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7b215-196">Google</span><span class="sxs-lookup"><span data-stu-id="7b215-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="7b215-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7b215-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7b215-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="7b215-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7b215-199">Örnek uygulamada, Google 'ın `userinfo.profile` kapsamı <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> , üzerinde çağrıldığında Framework tarafından otomatik olarak eklenir <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="7b215-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7b215-200">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7b215-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7b215-201">Aşağıdaki örnekte, Google `https://www.googleapis.com/auth/user.birthday.read` scope bir kullanıcının Doğum gününü almak için eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7b215-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7b215-202">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="7b215-202">Map user data keys and create claims</span></span>

<span data-ttu-id="7b215-203">Sağlayıcının seçeneklerinde, <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde bir veya her anahtar/alt anahtar için bir veya seçin.</span><span class="sxs-lookup"><span data-stu-id="7b215-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7b215-204">Talep türleri hakkında daha fazla bilgi için bkz <xref:System.Security.Claims.ClaimTypes> ..</span><span class="sxs-lookup"><span data-stu-id="7b215-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7b215-205">Örnek uygulama `urn:google:locale` `urn:google:picture` , `locale` Google Kullanıcı verilerinde ve anahtarlarından yerel ayar () ve resim () talepleri oluşturur `picture` :</span><span class="sxs-lookup"><span data-stu-id="7b215-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7b215-206">İçinde `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , bir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ), ile uygulamasında oturum açtı <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-206">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7b215-207">Oturum açma işlemi sırasında, ' <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> `ApplicationUser` dan kullanılabilir Kullanıcı verileri için bir talep saklayabilir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-207">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7b215-208">Örnek uygulamada `OnPostConfirmationAsync` ( *Account/externallogin. cshtml. cs* ), `urn:google:locale` `urn:google:picture` `ApplicationUser` için bir talep dahil olmak üzere, oturum açan için yerel ayar () ve resim () taleplerini belirler <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="7b215-208">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7b215-209">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulamasında depolanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="7b215-209">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="7b215-210">Kimlik doğrulaması :::no-loc(cookie)::: çok büyükse uygulamanın başarısız olmasına neden olabilir çünkü:</span><span class="sxs-lookup"><span data-stu-id="7b215-210">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7b215-211">Tarayıcı :::no-loc(cookie)::: üstbilginin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="7b215-211">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="7b215-212">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="7b215-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="7b215-213">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="7b215-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7b215-214">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="7b215-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7b215-215"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore>Kimlik doğrulama ara yazılımı için özel ' i kullanarak kimlik :::no-loc(Cookie)::: <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> istekleri arasında depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="7b215-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7b215-216">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="7b215-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7b215-217">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="7b215-217">Save the access token</span></span>

<span data-ttu-id="7b215-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin ' de depolanması gerekip gerekmediğini tanımlar <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="7b215-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7b215-219">`SaveTokens` , `false` son kimlik doğrulamanın boyutunu azaltmak için varsayılan olarak olarak ayarlanır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="7b215-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="7b215-220">Örnek uygulama, değerini içinde olarak ayarlar `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="7b215-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7b215-221">`OnPostConfirmationAsync`Yürütüldüğünde, erişim belirtecini ([Externalloginınfo. authenticationtokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)), içindeki dış sağlayıcıdan depolayın `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="7b215-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7b215-222">Örnek uygulama, erişim belirtecini `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) *hesabını Account/externallogin. cshtml. cs* içinde kaydeder:</span><span class="sxs-lookup"><span data-stu-id="7b215-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7b215-223">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="7b215-223">How to add additional custom tokens</span></span>

<span data-ttu-id="7b215-224">Bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için `SaveTokens` , örnek uygulama bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name için geçerli bir ile ekler <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="7b215-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7b215-225">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="7b215-225">Creating and adding claims</span></span>

<span data-ttu-id="7b215-226">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b215-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7b215-227">Daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> . ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="7b215-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7b215-228">Kullanıcılar <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> , Özet yönteminden türeterek ve uygulayarak özel eylemleri tanımlayabilir <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="7b215-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7b215-229">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7b215-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7b215-230">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="7b215-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="7b215-231">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , koleksiyondan verilen için tüm talep eylemlerini kaldırır <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="7b215-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7b215-232">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen bir talebini siler <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> .</span><span class="sxs-lookup"><span data-stu-id="7b215-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7b215-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> , protokol tarafından oluşturulan talepleri kaldırmak için öncelikle [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7b215-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7b215-234">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="7b215-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

## <a name="additional-resources"></a><span data-ttu-id="7b215-235">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7b215-235">Additional resources</span></span>

* <span data-ttu-id="7b215-236">[DotNet/aspnetcore mühendislik SocialSample uygulaması](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): bağlantılı örnek uygulama [DotNet/aspnetcore GitHub deposunun](https://github.com/dotnet/AspNetCore) `master` mühendislik dalında bulunur.</span><span class="sxs-lookup"><span data-stu-id="7b215-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="7b215-237">`master`Dal, ASP.NET Core sonraki sürümü için etkin geliştirme altında kod içerir.</span><span class="sxs-lookup"><span data-stu-id="7b215-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="7b215-238">Yayınlanmış bir ASP.NET Core sürümü için örnek uygulamanın bir sürümünü görmek için, **dal** açılan listesini kullanarak bir yayın dalı seçin (örneğin `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="7b215-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
