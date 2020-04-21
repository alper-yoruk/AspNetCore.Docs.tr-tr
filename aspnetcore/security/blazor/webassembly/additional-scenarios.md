---
title: ASP.NET Blazor Core WebAssembly ek güvenlik senaryoları
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661589"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="2db77-102">ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="2db77-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="2db77-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="2db77-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="2db77-104">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="2db77-104">Request additional access tokens</span></span>

<span data-ttu-id="2db77-105">Çoğu uygulama, yalnızca kullandıkları korumalı kaynaklarla etkileşim kurmak için bir erişim jetonu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2db77-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="2db77-106">Bazı senaryolarda, bir uygulamanın iki veya daha fazla kaynakla etkileşim kurabilmesi için birden fazla belirteç gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="2db77-107">Aşağıdaki örnekte, bir uygulamanın kullanıcı verilerini okuması ve posta göndermesi için ek Azure Etkin Dizin (AAD) Microsoft Graph API kapsamları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2db77-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="2db77-108">Azure AAD portalına Microsoft Graph API izinleri ekledikten sonra, ek kapsamlar`Program.Main`İstemci uygulamasında (, *Program.cs)* yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="2db77-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="2db77-109">Yöntem, `IAccessTokenProvider.RequestToken` bir uygulamanın aşağıdaki örnekte görüldüğü gibi, belirli bir kapsam kümesiyle bir belirteç sağlamasına olanak tanıyan aşırı yük sağlar:</span><span class="sxs-lookup"><span data-stu-id="2db77-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="2db77-110">`TryGetToken`Döndürür:</span><span class="sxs-lookup"><span data-stu-id="2db77-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="2db77-111">`true`kullanım `token` için.</span><span class="sxs-lookup"><span data-stu-id="2db77-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="2db77-112">`false`belirteç geri alınmazsa.</span><span class="sxs-lookup"><span data-stu-id="2db77-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="2db77-113">Belirteç isteği hatalarını işleme</span><span class="sxs-lookup"><span data-stu-id="2db77-113">Handle token request errors</span></span>

<span data-ttu-id="2db77-114">Tek Sayfalı Bir Uygulama (SPA) Open ID Connect (OIDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu SPA içinde ve Kimlik Sağlayıcısı'nda (IP) kullanıcının kimlik bilgilerini sağlaması sonucu ayarlanan oturum çerezi biçiminde yerel olarak tutulur.</span><span class="sxs-lookup"><span data-stu-id="2db77-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="2db77-115">Kullanıcı için IP'nin yadığı belirteçler genellikle kısa süreler için geçerlidir, normalde yaklaşık bir saat, bu nedenle istemci uygulaması düzenli olarak yeni belirteçler getirmelidir.</span><span class="sxs-lookup"><span data-stu-id="2db77-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="2db77-116">Aksi takdirde, verilen belirteçlerin süresi dolduktan sonra kullanıcı oturumdan çıkar.</span><span class="sxs-lookup"><span data-stu-id="2db77-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="2db77-117">Çoğu durumda, OIDC istemcileri IP içinde tutulan kimlik doğrulama durumu veya "oturum" sayesinde kullanıcının yeniden kimlik doğrulaması gerektirmeden yeni belirteçler sağlama da mümkün.</span><span class="sxs-lookup"><span data-stu-id="2db77-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="2db77-118">Örneğin, kullanıcı herhangi bir nedenle IP'den açıkça oturum açtıklarında, istemcinin kullanıcı etkileşimi olmadan bir belirteç alabildiği bazı durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="2db77-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="2db77-119">Bu senaryo, bir kullanıcı `https://login.microsoftonline.com` ziyaret edip oturumu kaplarsa oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığını hemen bilmez. İstemcinin tuttuğu herhangi bir belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="2db77-120">Ayrıca, istemci geçerli belirteç sona erdikten sonra kullanıcı etkileşimi olmadan yeni bir belirteç sağlamak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="2db77-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="2db77-121">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="2db77-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="2db77-122">Onlar SPA'ların doğasının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="2db77-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="2db77-123">Tanımlama bilgileri kullanan bir SPA, kimlik doğrulama çerezi kaldırılırsa sunucu API'sini de çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="2db77-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="2db77-124">Bir uygulama korumalı kaynaklara API çağrıları gerçekleştirdiğinde, aşağıdakilerden haberdar olmalısınız:</span><span class="sxs-lookup"><span data-stu-id="2db77-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="2db77-125">API'yi aramak için yeni bir erişim belirteci sağlamak için, kullanıcının yeniden kimlik doğrulaması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="2db77-126">İstemci geçerli gibi görünen bir belirteci olsa bile, belirteç kullanıcı tarafından iptal edildiğiiçin sunucuya yapılan çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="2db77-127">Uygulama bir belirteç istediğinde, iki olası sonucu vardır:</span><span class="sxs-lookup"><span data-stu-id="2db77-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="2db77-128">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="2db77-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="2db77-129">İstek başarısız olur ve uygulamanın yeni bir belirteç elde etmek için kullanıcının kimliğini yeniden doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2db77-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="2db77-130">Belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğiniz konusunda karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="2db77-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="2db77-131">Çeşitli yaklaşımlar karmaşıklık artan düzeylerde var:</span><span class="sxs-lookup"><span data-stu-id="2db77-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="2db77-132">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="2db77-132">Store the current page state in session storage.</span></span> <span data-ttu-id="2db77-133">Sırasında, `OnInitializeAsync`devam etmeden önce durum geri yüklenebilir olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="2db77-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="2db77-134">Bir sorgu dize parametresi ekleyin ve uygulamanın önceden kaydedilmiş durumu yeniden nemlendirmesi için gereken sinyali vermek için bunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="2db77-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="2db77-135">Diğer öğelerle çakışma riski olmadan verileri oturum depolama alanında depolamak için benzersiz bir tanımlayıcıiçeren bir sorgu dize parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2db77-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="2db77-136">Aşağıdaki örnek, nasıl yapılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2db77-136">The following example shows how to:</span></span>

* <span data-ttu-id="2db77-137">Oturum açma sayfasına yönlendirmeden önce durumu koruyun.</span><span class="sxs-lookup"><span data-stu-id="2db77-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="2db77-138">Sorgu dizesi parametresini kullanarak önceki durumu sonra kimlik doğrulaması kurtarın.</span><span class="sxs-lookup"><span data-stu-id="2db77-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="2db77-139">Kimlik doğrulama işleminden önce uygulama durumunu kaydetme</span><span class="sxs-lookup"><span data-stu-id="2db77-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="2db77-140">Kimlik doğrulama işlemi sırasında, tarayıcı IP'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="2db77-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="2db77-141">Bu durum, durum kapsayıcısı gibi bir şey kullanıyorsanız ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde de geçerli olabilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="2db77-142">Uygulamaya özgü durumu veya başvuruyu korumak için özel bir kimlik doğrulama durumu nesnesi kullanabilir ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db77-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="2db77-143">`Authentication`bileşen (*Sayfa/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="2db77-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="2db77-144">Uygulama rotalarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2db77-144">Customize app routes</span></span>

<span data-ttu-id="2db77-145">Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2db77-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="2db77-146">Yol</span><span class="sxs-lookup"><span data-stu-id="2db77-146">Route</span></span>                            | <span data-ttu-id="2db77-147">Amaç</span><span class="sxs-lookup"><span data-stu-id="2db77-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="2db77-148">Oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="2db77-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="2db77-149">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="2db77-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="2db77-150">Oturum açma işlemi bazı nedenlerden dolayı başarısız olduğunda hata iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2db77-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="2db77-151">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="2db77-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="2db77-152">Bir oturum son işlem sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="2db77-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="2db77-153">Oturum açma işlemi bazı nedenlerden dolayı başarısız olduğunda hata iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2db77-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="2db77-154">Kullanıcının başarılı bir şekilde oturumu nettigini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2db77-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="2db77-155">Kullanıcı profilini yeniden düzenledirecek bir işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="2db77-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="2db77-156">Yeni bir kullanıcıyı kaydetmek için bir işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="2db77-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="2db77-157">Önceki tabloda gösterilen yollar . `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`</span><span class="sxs-lookup"><span data-stu-id="2db77-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="2db77-158">Özel rotalar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir rotası olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2db77-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="2db77-159">Aşağıdaki örnekte, tüm yollar `/security`.</span><span class="sxs-lookup"><span data-stu-id="2db77-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="2db77-160">`Authentication`bileşen (*Sayfa/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="2db77-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="2db77-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2db77-161">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="2db77-162">Gereksinim tamamen farklı yollar gerektiriyorsa, yolları daha önce `RemoteAuthenticatorView` açıklandığı gibi ayarlayın ve açık bir eylem parametresi ile işiçin:</span><span class="sxs-lookup"><span data-stu-id="2db77-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="2db77-163">İsterseniz ui'yi farklı sayfalara ayırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db77-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="2db77-164">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2db77-164">Customize the authentication user interface</span></span>

<span data-ttu-id="2db77-165">`RemoteAuthenticatorView`her kimlik doğrulama durumu için varsayılan bir ui parçası kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="2db77-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="2db77-166">Her durum özel olarak geçirilerek `RenderFragment`özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2db77-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="2db77-167">İlk oturum açma işlemi sırasında görüntülenen metni özelleştirmek `RemoteAuthenticatorView` için aşağıdaki gibi değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db77-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="2db77-168">`Authentication`bileşen (*Sayfa/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="2db77-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="2db77-169">Aşağıdaki `RemoteAuthenticatorView` tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçası vardır.</span><span class="sxs-lookup"><span data-stu-id="2db77-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="2db77-170">Yol</span><span class="sxs-lookup"><span data-stu-id="2db77-170">Route</span></span>                            | <span data-ttu-id="2db77-171">Parça</span><span class="sxs-lookup"><span data-stu-id="2db77-171">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
