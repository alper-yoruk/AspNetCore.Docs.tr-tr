---
title: ASP.NET Core Blazor weelsembly ek güvenlik senaryoları
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111181"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="48d8a-102">ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="48d8a-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="48d8a-103">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="48d8a-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="48d8a-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="48d8a-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="48d8a-106">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="48d8a-106">Request additional access tokens</span></span>

<span data-ttu-id="48d8a-107">Çoğu uygulama yalnızca kullandıkları korunan kaynaklarla etkileşim kurmak için bir erişim belirteci gerektirir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-107">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="48d8a-108">Bazı senaryolarda, bir uygulama iki veya daha fazla kaynakla etkileşim kurmak için birden fazla belirteç gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-108">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="48d8a-109">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-109">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="48d8a-110">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, istemci uygulamasında ek kapsamlar yapılandırılır (`Program.Main`, *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="48d8a-110">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="48d8a-111">`IAccessTokenProvider.RequestToken` Yöntemi, bir uygulamanın aşağıdaki örnekte görüldüğü gibi, belirli bir kapsam kümesiyle belirteç sağlamasını sağlayan bir aşırı yükleme sağlar:</span><span class="sxs-lookup"><span data-stu-id="48d8a-111">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="48d8a-112">`TryGetToken`döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="48d8a-112">`TryGetToken` returns:</span></span>

* <span data-ttu-id="48d8a-113">`true``token` ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-113">`true` with the `token` for use.</span></span>
* <span data-ttu-id="48d8a-114">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="48d8a-114">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="48d8a-115">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="48d8a-115">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="48d8a-116">Hizmeti `AuthorizationMessageHandler` , giden isteklere erişim belirteçleri `HttpClient` eklemek için ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-116">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="48d8a-117">Belirteçler, mevcut `IAccessTokenProvider` hizmet kullanılarak alınır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-117">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="48d8a-118">Bir belirteç alınamadığından, bir `AccessTokenNotAvailableException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="48d8a-118">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="48d8a-119">`AccessTokenNotAvailableException`, yeni `Redirect` bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-119">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="48d8a-120">, `AuthorizationMessageHandler` `ConfigureHandler` Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-120">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="48d8a-121">Aşağıdaki `AuthorizationMessageHandler` örnekte, `HttpClient` içinde `Program.Main` bir (*program.cs*) yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="48d8a-121">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="48d8a-122">Kolaylık olması için, `BaseAddressAuthorizationMessageHandler` bır yetkili URL olarak uygulama temel adresiyle önceden yapılandırılmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-122">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="48d8a-123">Kimlik doğrulaması etkinleştirilmiş Blazor WebAssembly şablonları artık [ıhttpclientfactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) kullanarak bir `HttpClient` ile ayarlama yapın `BaseAddressAuthorizationMessageHandler`:</span><span class="sxs-lookup"><span data-stu-id="48d8a-123">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="48d8a-124">Önceki örnekte istemci oluşturulduğu `CreateClient` yerde, `HttpClient` sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-124">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="48d8a-125">Daha sonra `HttpClient` , yapılandırılmış bir basit `try-catch` model kullanarak yetkili istekler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-125">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="48d8a-126">Aşağıdaki `FetchData` bileşen, hava durumu tahmin verilerini ister:</span><span class="sxs-lookup"><span data-stu-id="48d8a-126">The following `FetchData` component requests weather forecast data:</span></span>

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

<span data-ttu-id="48d8a-127">Alternatif olarak, tek bir sınıftaki tüm HTTP ve belirteç alımı sorunlarını ele alan yazılı bir istemci tanımlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="48d8a-127">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="48d8a-128">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="48d8a-128">*WeatherClient.cs*:</span></span>

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

<span data-ttu-id="48d8a-129">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="48d8a-129">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="48d8a-130">*Fetchdata. Razor*:</span><span class="sxs-lookup"><span data-stu-id="48d8a-130">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="48d8a-131">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="48d8a-131">Handle token request errors</span></span>

<span data-ttu-id="48d8a-132">Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve kimlik sağlayıcısı 'nda (IP) kimlik bilgilerini sunan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="48d8a-132">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="48d8a-133">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-133">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="48d8a-134">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-134">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="48d8a-135">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="48d8a-135">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="48d8a-136">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="48d8a-136">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="48d8a-137">Bu senaryo, bir kullanıcı tarafından ziyaret `https://login.microsoftonline.com` ettiğinde ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-137">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="48d8a-138">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="48d8a-138">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="48d8a-139">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-139">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="48d8a-140">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-140">They are part of the nature of SPAs.</span></span> <span data-ttu-id="48d8a-141">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="48d8a-141">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="48d8a-142">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="48d8a-142">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="48d8a-143">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-143">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="48d8a-144">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-144">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="48d8a-145">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="48d8a-145">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="48d8a-146">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-146">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="48d8a-147">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-147">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="48d8a-148">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-148">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="48d8a-149">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="48d8a-149">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="48d8a-150">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-150">Store the current page state in session storage.</span></span> <span data-ttu-id="48d8a-151">Sırasında `OnInitializeAsync`, devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="48d8a-151">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="48d8a-152">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-152">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="48d8a-153">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="48d8a-153">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="48d8a-154">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="48d8a-154">The following example shows how to:</span></span>

* <span data-ttu-id="48d8a-155">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="48d8a-155">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="48d8a-156">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-156">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="48d8a-157">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="48d8a-157">Save app state before an authentication operation</span></span>

<span data-ttu-id="48d8a-158">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-158">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="48d8a-159">Durum kapsayıcısı gibi bir şey kullandığınızda ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-159">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="48d8a-160">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48d8a-160">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="48d8a-161">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="48d8a-161">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="48d8a-162">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="48d8a-162">Customize app routes</span></span>

<span data-ttu-id="48d8a-163">Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-163">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="48d8a-164">Yol</span><span class="sxs-lookup"><span data-stu-id="48d8a-164">Route</span></span>                            | <span data-ttu-id="48d8a-165">Amaç</span><span class="sxs-lookup"><span data-stu-id="48d8a-165">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="48d8a-166">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-166">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="48d8a-167">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-167">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="48d8a-168">Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-168">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="48d8a-169">Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-169">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="48d8a-170">Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-170">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="48d8a-171">Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-171">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="48d8a-172">Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-172">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="48d8a-173">Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-173">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="48d8a-174">Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="48d8a-174">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="48d8a-175">Yukarıdaki tabloda gösterilen rotalar aracılığıyla `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-175">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="48d8a-176">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-176">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="48d8a-177">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security`.</span><span class="sxs-lookup"><span data-stu-id="48d8a-177">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="48d8a-178">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="48d8a-178">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="48d8a-179">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="48d8a-179">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="48d8a-180">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini açık bir eylem parametresiyle `RemoteAuthenticatorView` işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="48d8a-180">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="48d8a-181">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-181">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="48d8a-182">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="48d8a-182">Customize the authentication user interface</span></span>

<span data-ttu-id="48d8a-183">`RemoteAuthenticatorView`Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-183">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="48d8a-184">Her durum, özel `RenderFragment`olarak geçirerek özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-184">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="48d8a-185">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için aşağıdaki `RemoteAuthenticatorView` gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-185">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="48d8a-186">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="48d8a-186">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="48d8a-187">, `RemoteAuthenticatorView` Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-187">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="48d8a-188">Yol</span><span class="sxs-lookup"><span data-stu-id="48d8a-188">Route</span></span>                            | <span data-ttu-id="48d8a-189">Parça</span><span class="sxs-lookup"><span data-stu-id="48d8a-189">Fragment</span></span>                |
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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="48d8a-190">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="48d8a-190">Support prerendering with authentication</span></span>

<span data-ttu-id="48d8a-191">Barındırılan Blazor webassembly uygulama konularından birindeki yönergeleri uyguladıktan sonra aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="48d8a-191">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="48d8a-192">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="48d8a-192">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="48d8a-193">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-193">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="48d8a-194">Istemci uygulamasının `Program` sınıfında (*program.cs*), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin, `ConfigureCommonServices`) göre çarpanlara ayırın:</span><span class="sxs-lookup"><span data-stu-id="48d8a-194">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="48d8a-195">Sunucu `Startup.ConfigureServices`uygulamasında, aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="48d8a-195">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="48d8a-196">Sunucu uygulamasının `Startup.Configure` yönteminde, ile `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`değiştirin:</span><span class="sxs-lookup"><span data-stu-id="48d8a-196">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="48d8a-197">Sunucu uygulamasında, yoksa bir *sayfa* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="48d8a-197">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="48d8a-198">Sunucu uygulamasının *Sayfalar* klasörünün içinde bir *_Host. cshtml* sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="48d8a-198">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="48d8a-199">İçeriği Istemci uygulamasının *Wwwroot/index.html* dosyasından *Sayfalar/_Host. cshtml* dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-199">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="48d8a-200">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="48d8a-200">Update the file's contents:</span></span>

* <span data-ttu-id="48d8a-201">Dosyanın `@page "_Host"` en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="48d8a-201">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="48d8a-202">`<app>Loading...</app>` Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="48d8a-202">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="48d8a-203">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="48d8a-203">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="48d8a-204">Bir üçüncü taraf sağlayıcı ile barındırılan Blazor webassembly uygulamasının kimliğini doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-204">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="48d8a-205">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-205">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="48d8a-206">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="48d8a-206">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="48d8a-207">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="48d8a-207">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="48d8a-208">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="48d8a-208">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="48d8a-209">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="48d8a-209">In this scenario:</span></span>

* <span data-ttu-id="48d8a-210">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="48d8a-210">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="48d8a-211">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="48d8a-211">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="48d8a-212">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-212">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="48d8a-213">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="48d8a-213">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="48d8a-214">Kimliği bir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-214">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="48d8a-215">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-215">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="48d8a-216">Bir Kullanıcı oturum açtığında kimlik doğrulama işleminin bir parçası olarak, kimlik, erişimi toplar ve belirteçleri yenileyebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-216">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="48d8a-217">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-217">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="48d8a-218">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="48d8a-218">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="48d8a-219">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-219">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="48d8a-220">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcideki kimlikle çağırmak için üçüncü taraf erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-220">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="48d8a-221">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="48d8a-221">We don't recommend this approach.</span></span> <span data-ttu-id="48d8a-222">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-222">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="48d8a-223">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="48d8a-223">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="48d8a-224">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="48d8a-224">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="48d8a-225">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-225">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="48d8a-226">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="48d8a-226">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="48d8a-227">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="48d8a-227">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="48d8a-228">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="48d8a-228">Make an API call from the client to the server API.</span></span> <span data-ttu-id="48d8a-229">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="48d8a-229">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="48d8a-230">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="48d8a-230">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="48d8a-231">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-231">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="48d8a-232">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="48d8a-232">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
