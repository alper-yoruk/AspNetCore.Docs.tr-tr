---
title: ASP.NET Core Blazor weelsembly ek güvenlik senaryoları
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: cd1433d5716b9b595270209fa874a8cb93fdf699
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138436"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="f427c-102">ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="f427c-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="f427c-103">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f427c-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="f427c-104">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="f427c-104">Request additional access tokens</span></span>

<span data-ttu-id="f427c-105">Çoğu uygulama yalnızca kullandıkları korunan kaynaklarla etkileşim kurmak için bir erişim belirteci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f427c-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="f427c-106">Bazı senaryolarda, bir uygulama iki veya daha fazla kaynakla etkileşim kurmak için birden fazla belirteç gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="f427c-107">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="f427c-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="f427c-108">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, istemci uygulamasında ek kapsamlar yapılandırılır (`Program.Main`, *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f427c-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="f427c-109">`IAccessTokenProvider.RequestToken` Yöntemi, bir uygulamanın aşağıdaki örnekte görüldüğü gibi, belirli bir kapsam kümesiyle bir erişim belirteci sağlamasına izin veren bir aşırı yükleme sağlar:</span><span class="sxs-lookup"><span data-stu-id="f427c-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="f427c-110">`TryGetToken`döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="f427c-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="f427c-111">`true``token` ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="f427c-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="f427c-112">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="f427c-112">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="f427c-113">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="f427c-113">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="f427c-114">Hizmeti `AuthorizationMessageHandler` , giden isteklere erişim belirteçleri `HttpClient` eklemek için ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-114">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="f427c-115">Belirteçler, mevcut `IAccessTokenProvider` hizmet kullanılarak alınır.</span><span class="sxs-lookup"><span data-stu-id="f427c-115">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="f427c-116">Bir belirteç alınamadığından, bir `AccessTokenNotAvailableException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f427c-116">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="f427c-117">`AccessTokenNotAvailableException`, yeni `Redirect` bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f427c-117">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="f427c-118">, `AuthorizationMessageHandler` `ConfigureHandler` Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-118">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="f427c-119">Aşağıdaki `AuthorizationMessageHandler` örnekte, `HttpClient` içinde `Program.Main` bir (*program.cs*) yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f427c-119">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="f427c-120">Kolaylık olması için, `BaseAddressAuthorizationMessageHandler` bır yetkili URL olarak uygulama temel adresiyle önceden yapılandırılmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="f427c-120">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="f427c-121">Kimlik doğrulaması etkinleştirilmiş Blazor WebAssembly şablonları artık [ıhttpclientfactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) kullanarak bir `HttpClient` ile ayarlama yapın `BaseAddressAuthorizationMessageHandler`:</span><span class="sxs-lookup"><span data-stu-id="f427c-121">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="f427c-122">Önceki örnekte istemci oluşturulduğu `CreateClient` yerde, `HttpClient` sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f427c-122">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f427c-123">Daha sonra `HttpClient` , yapılandırılmış bir basit `try-catch` model kullanarak yetkili istekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f427c-123">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="f427c-124">Aşağıdaki `FetchData` bileşen, hava durumu tahmin verilerini ister:</span><span class="sxs-lookup"><span data-stu-id="f427c-124">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="f427c-125">Alternatif olarak, tek bir sınıftaki tüm HTTP ve belirteç alımı sorunlarını ele alan yazılı bir istemci tanımlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f427c-125">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="f427c-126">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="f427c-126">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="f427c-127">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f427c-127">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="f427c-128">*Fetchdata. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f427c-128">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="f427c-129">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="f427c-129">Handle token request errors</span></span>

<span data-ttu-id="f427c-130">Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve kimlik sağlayıcısı 'nda (IP) kimlik bilgilerini sunan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="f427c-130">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="f427c-131">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f427c-131">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="f427c-132">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-132">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="f427c-133">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="f427c-133">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="f427c-134">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="f427c-134">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="f427c-135">Bu senaryo, bir kullanıcı tarafından ziyaret `https://login.microsoftonline.com` ettiğinde ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-135">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="f427c-136">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="f427c-136">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="f427c-137">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="f427c-137">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="f427c-138">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="f427c-138">They are part of the nature of SPAs.</span></span> <span data-ttu-id="f427c-139">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="f427c-139">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="f427c-140">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="f427c-140">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="f427c-141">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-141">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="f427c-142">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-142">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="f427c-143">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="f427c-143">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="f427c-144">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="f427c-144">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="f427c-145">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f427c-145">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="f427c-146">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f427c-146">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="f427c-147">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="f427c-147">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="f427c-148">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="f427c-148">Store the current page state in session storage.</span></span> <span data-ttu-id="f427c-149">Sırasında `OnInitializeAsync`, devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f427c-149">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="f427c-150">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="f427c-150">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="f427c-151">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f427c-151">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="f427c-152">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f427c-152">The following example shows how to:</span></span>

* <span data-ttu-id="f427c-153">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="f427c-153">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="f427c-154">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="f427c-154">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="f427c-155">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="f427c-155">Save app state before an authentication operation</span></span>

<span data-ttu-id="f427c-156">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="f427c-156">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="f427c-157">Durum kapsayıcısı gibi bir şey kullandığınızda ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-157">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="f427c-158">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f427c-158">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="f427c-159">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="f427c-159">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="f427c-160">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f427c-160">Customize app routes</span></span>

<span data-ttu-id="f427c-161">Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="f427c-161">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="f427c-162">Yol</span><span class="sxs-lookup"><span data-stu-id="f427c-162">Route</span></span>                            | <span data-ttu-id="f427c-163">Amaç</span><span class="sxs-lookup"><span data-stu-id="f427c-163">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="f427c-164">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="f427c-164">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="f427c-165">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="f427c-165">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="f427c-166">Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f427c-166">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="f427c-167">Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="f427c-167">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="f427c-168">Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="f427c-168">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="f427c-169">Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f427c-169">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="f427c-170">Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f427c-170">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="f427c-171">Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="f427c-171">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="f427c-172">Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="f427c-172">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="f427c-173">Yukarıdaki tabloda gösterilen rotalar aracılığıyla `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-173">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="f427c-174">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f427c-174">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="f427c-175">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security`.</span><span class="sxs-lookup"><span data-stu-id="f427c-175">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="f427c-176">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="f427c-176">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f427c-177">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f427c-177">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="f427c-178">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini açık bir eylem parametresiyle `RemoteAuthenticatorView` işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="f427c-178">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="f427c-179">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-179">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="f427c-180">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f427c-180">Customize the authentication user interface</span></span>

<span data-ttu-id="f427c-181">`RemoteAuthenticatorView`Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f427c-181">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="f427c-182">Her durum, özel `RenderFragment`olarak geçirerek özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-182">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="f427c-183">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için aşağıdaki `RemoteAuthenticatorView` gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-183">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="f427c-184">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="f427c-184">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="f427c-185">, `RemoteAuthenticatorView` Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f427c-185">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="f427c-186">Yol</span><span class="sxs-lookup"><span data-stu-id="f427c-186">Route</span></span>                            | <span data-ttu-id="f427c-187">Parça</span><span class="sxs-lookup"><span data-stu-id="f427c-187">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="f427c-188">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f427c-188">Customize the user</span></span>

<span data-ttu-id="f427c-189">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-189">Users bound to the app can be customized.</span></span> <span data-ttu-id="f427c-190">Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar kullanıcının kimlik doğrulama `amr` yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="f427c-190">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="f427c-191">`RemoteUserAccount` Sınıfı genişleten bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f427c-191">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="f427c-192">Şunu genişleten `AccountClaimsPrincipalFactory<TAccount>`bir fabrika oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f427c-192">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public AccountClaimsPrincipalFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="f427c-193">`CustomAccountFactory`Aşağıdakileri kullanmak için Hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f427c-193">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="f427c-194">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="f427c-194">Support prerendering with authentication</span></span>

<span data-ttu-id="f427c-195">Barındırılan Blazor webassembly uygulama konularından birindeki yönergeleri uyguladıktan sonra aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f427c-195">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="f427c-196">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="f427c-196">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="f427c-197">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="f427c-197">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="f427c-198">Istemci uygulamasının `Program` sınıfında (*program.cs*), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin, `ConfigureCommonServices`) göre çarpanlara ayırın:</span><span class="sxs-lookup"><span data-stu-id="f427c-198">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

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

<span data-ttu-id="f427c-199">Sunucu `Startup.ConfigureServices`uygulamasında, aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f427c-199">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="f427c-200">Sunucu uygulamasının `Startup.Configure` yönteminde, ile `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f427c-200">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="f427c-201">Sunucu uygulamasında, yoksa bir *sayfa* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f427c-201">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="f427c-202">Sunucu uygulamasının *Sayfalar* klasörünün içinde bir *_Host. cshtml* sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f427c-202">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="f427c-203">İçeriği Istemci uygulamasının *Wwwroot/index.html* dosyasından *Sayfalar/_Host. cshtml* dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f427c-203">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="f427c-204">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f427c-204">Update the file's contents:</span></span>

* <span data-ttu-id="f427c-205">Dosyanın `@page "_Host"` en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f427c-205">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="f427c-206">`<app>Loading...</app>` Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f427c-206">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="f427c-207">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="f427c-207">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="f427c-208">Bir üçüncü taraf sağlayıcı ile barındırılan Blazor webassembly uygulamasının kimliğini doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="f427c-208">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="f427c-209">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f427c-209">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="f427c-210">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f427c-210">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="f427c-211">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="f427c-211">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="f427c-212">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f427c-212">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="f427c-213">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="f427c-213">In this scenario:</span></span>

* <span data-ttu-id="f427c-214">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="f427c-214">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="f427c-215">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="f427c-215">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="f427c-216">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-216">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="f427c-217">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="f427c-217">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="f427c-218">Kimliği bir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f427c-218">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="f427c-219">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="f427c-219">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="f427c-220">Bir Kullanıcı oturum açtığında kimlik doğrulama işleminin bir parçası olarak, kimlik, erişimi toplar ve belirteçleri yenileyebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-220">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="f427c-221">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="f427c-221">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="f427c-222">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="f427c-222">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="f427c-223">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f427c-223">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="f427c-224">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcideki kimlikle çağırmak için üçüncü taraf erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f427c-224">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="f427c-225">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="f427c-225">We don't recommend this approach.</span></span> <span data-ttu-id="f427c-226">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f427c-226">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="f427c-227">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f427c-227">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="f427c-228">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="f427c-228">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="f427c-229">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-229">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="f427c-230">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="f427c-230">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="f427c-231">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="f427c-231">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="f427c-232">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="f427c-232">Make an API call from the client to the server API.</span></span> <span data-ttu-id="f427c-233">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="f427c-233">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="f427c-234">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="f427c-234">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="f427c-235">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-235">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="f427c-236">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f427c-236">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
