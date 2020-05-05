---
title: ASP.NET Core Blazor weelsembly ek güvenlik senaryoları
author: guardrex
description: Webassembly 'ın Blazor ek güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e69b598431027aa540227b87dedfd091057a1af4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768175"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Giden isteklere belirteç iliştirme

Hizmeti `AuthorizationMessageHandler` , giden isteklere erişim belirteçleri `HttpClient` eklemek için ile birlikte kullanılabilir. Belirteçler, mevcut `IAccessTokenProvider` hizmet kullanılarak alınır. Bir belirteç alınamadığından, bir `AccessTokenNotAvailableException` oluşturulur. `AccessTokenNotAvailableException`, yeni `Redirect` bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir. , `AuthorizationMessageHandler` `ConfigureHandler` Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir.

Aşağıdaki `AuthorizationMessageHandler` örnekte, `HttpClient` içinde `Program.Main` bir (*program.cs*) yapılandırır:

```csharp
builder.Services.AddTransient(sp =>
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

Kolaylık olması için, `BaseAddressAuthorizationMessageHandler` bır yetkili URL olarak uygulama temel adresiyle önceden yapılandırılmış bir içerir. Kimlik doğrulaması etkinleştirilmiş Blazor WebAssembly şablonları artık [ıhttpclientfactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) kullanarak bir `HttpClient` ile ayarlama yapın `BaseAddressAuthorizationMessageHandler`:

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

Önceki örnekte istemci oluşturulduğu `CreateClient` yerde, `HttpClient` sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.

Daha sonra `HttpClient` , yapılandırılmış bir basit `try-catch` model kullanarak yetkili istekler oluşturmak için kullanılır. Aşağıdaki `FetchData` bileşen, hava durumu tahmin verilerini ister:

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

Alternatif olarak, tek bir sınıftaki tüm HTTP ve belirteç alımı sorunlarını ele alan yazılı bir istemci tanımlayabilirsiniz:

*WeatherClient.cs*:

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

*Program.cs*:

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*Fetchdata. Razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a>Ek erişim belirteçleri isteyin

Erişim belirteçleri, çağırarak `IAccessTokenProvider.RequestAccessToken`el ile elde edilebilir.

Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir. Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, istemci uygulamasında ek kapsamlar yapılandırılır (`Program.Main`, *program.cs*):

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

`IAccessTokenProvider.RequestToken` Yöntemi, bir uygulamanın aşağıdaki örnekte görüldüğü gibi, belirli bir kapsam kümesiyle bir erişim belirteci sağlamasına izin veren bir aşırı yükleme sağlar:

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

`TryGetToken`döndürdüğü

* `true``token` ' i kullanın.
* `false`belirteç alınmadıysa.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>API istek seçeneklerini getiren HttpClient ve HttpRequestMessage

Blazor WebAssembly uygulamasında WebAssembly üzerinde çalışırken, [HttpClient](xref:fundamentals/http-requests) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir. Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz. Aşağıdaki örnek, sunucuda Yapılacaklar `POST` listesi API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

.NET WebAssembly 'ın uygulanması, `HttpClient` [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır. Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır. 

HTTP getirme isteği seçenekleri, aşağıdaki tabloda gösterilen `HttpRequestMessage` uzantı yöntemleriyle yapılandırılabilir.

| `HttpRequestMessage`genişletme yöntemi | Fetch isteği özelliği |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [Credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [önbellek](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [modundaysa](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [doğruluğunu](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Daha genel `SetBrowserRequestOption` genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz.
 
HTTP yanıtı, yanıt içeriğindeki eşitleme okuma desteğini etkinleştirmek için tipik olarak bir Blazor WebAssembly uygulamasında arabelleğe kaydedilir. Yanıt akışı desteğini etkinleştirmek için istekteki `SetBrowserResponseStreamingEnabled` genişletme yöntemini kullanın.

Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, `SetBrowserRequestCredentials` genişletme yöntemini kullanın:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, `Authorization` bir üst bilgiye CORS ilkesi tarafından izin verilmelidir.

Aşağıdaki ilke için yapılandırma içerir:

* İstek kaynakları (`http://localhost:5000`, `https://localhost:5001`).
* Any yöntemi (fiil).
* `Content-Type`ve `Authorization` üst bilgiler. Özel bir üstbilgiye (örneğin, `x-custom-header`) izin vermek için, çağrılırken <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>üstbilgiyi listeleyin.
* İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri (`credentials` özellik olarak `include`ayarlanır).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Daha fazla bilgi için, <xref:security/cors> bkz. ve örnek uygulamanın http isteği Sınayıcısı bileşeni (*Bileşenler/httprequesttester. Razor*).

## <a name="handle-token-request-errors"></a>Tanıtıcı belirteci isteği hataları

Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve kimlik sağlayıcısı 'nda (IP) kimlik bilgilerini sunan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.

IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir. Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir. Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.

İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı. Bu senaryo, bir kullanıcı tarafından ziyaret `https://login.microsoftonline.com` ettiğinde ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir. Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.

Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir. Bunlar, maça doğaları 'nın bir parçasıdır. Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.

Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:

* API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.
* İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.

Uygulama bir belirteç istediğinde, olası iki sonuç vardır:

* İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.
* İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.

Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir. Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:

* Geçerli sayfa durumunu oturum depolama alanında depolayın. Sırasında `OnInitializeAsync`, devam etmeden önce durumun geri yüklenebildiğinden emin olun.
* Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.
* Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.

Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

* Oturum açma sayfasına yönlendirmeden önce durumu koru.
* Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet

Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır. Durum kapsayıcısı gibi bir şey kullandığınızda ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir. Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.

`Authentication`bileşen (*Sayfalar/Authentication. Razor*):

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

## <a name="customize-app-routes"></a>Uygulama yollarını özelleştirme

Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.

| Yol                            | Amaç |
| -------------------------------- | ------- |
| `authentication/login`           | Bir oturum açma işlemini tetikler. |
| `authentication/login-callback`  | Herhangi bir oturum açma işleminin sonucunu işler. |
| `authentication/login-failed`    | Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler. |
| `authentication/logout`          | Bir oturum kapatma işlemi tetikler. |
| `authentication/logout-callback` | Bir oturum kapatma işleminin sonucunu işler. |
| `authentication/logout-failed`   | Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler. |
| `authentication/logged-out`      | Kullanıcının oturumu başarıyla sonlandırdığını gösterir. |
| `authentication/profile`         | Kullanıcı profilini düzenlemek için bir işlem tetikler. |
| `authentication/register`        | Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler. |

Yukarıdaki tabloda gösterilen rotalar aracılığıyla `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`yapılandırılabilir. Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.

Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security`.

`Authentication`bileşen (*Sayfalar/Authentication. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini açık bir eylem parametresiyle `RemoteAuthenticatorView` işleme yapın:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.

## <a name="customize-the-authentication-user-interface"></a>Kimlik doğrulama kullanıcı arabirimini özelleştirme

`RemoteAuthenticatorView`Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir. Her durum, özel `RenderFragment`olarak geçirerek özelleştirilebilir. İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için aşağıdaki `RemoteAuthenticatorView` gibi değiştirebilir.

`Authentication`bileşen (*Sayfalar/Authentication. Razor*):

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

, `RemoteAuthenticatorView` Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.

| Yol                            | Parça                |
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

## <a name="customize-the-user"></a>Kullanıcıyı özelleştirme

Uygulamayla bağlantılı kullanıcılar özelleştirilebilir. Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar kullanıcının kimlik doğrulama `amr` yöntemlerinin her biri için bir talep alır.

`RemoteUserAccount` Sınıfı genişleten bir sınıf oluşturun:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Şunu genişleten `AccountClaimsPrincipalFactory<TAccount>`bir fabrika oluşturun:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
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

`CustomAccountFactory`Aşağıdakileri kullanmak için Hizmetleri kaydedin:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a>Kimlik doğrulaması ile prerendering desteği

Barındırılan Blazor webassembly uygulama konularından birindeki yönergeleri uyguladıktan sonra aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:

* Yetkilendirmenin gerekmediği ön ekler yolları.
* Yetkilendirme gerekli olan PreRender yolları değildir.

Istemci uygulamasının `Program` sınıfında (*program.cs*), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin, `ConfigureCommonServices`) göre çarpanlara ayırın:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

Sunucu `Startup.ConfigureServices`uygulamasında, aşağıdaki ek hizmetleri kaydedin:

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

Sunucu uygulamasının `Startup.Configure` yönteminde, ile `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`değiştirin:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Sunucu uygulamasında, yoksa bir *sayfa* klasörü oluşturun. Sunucu uygulamasının *Sayfalar* klasörünün içinde bir *_Host. cshtml* sayfası oluşturun. İçeriği Istemci uygulamasının *Wwwroot/index.html* dosyasından *Sayfalar/_Host. cshtml* dosyasına yapıştırın. Dosyanın içeriğini güncelleştirin:

* Dosyanın `@page "_Host"` en üstüne ekleyin.
* `<app>Loading...</app>` Etiketi aşağıdaki kodla değiştirin:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler

Bir üçüncü taraf sağlayıcı ile barındırılan Blazor webassembly uygulamasının kimliğini doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır. Seçtiğiniz bir senaryo, senaryonuza bağlıdır.

Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama

Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Bu senaryoda:

* Uygulamayı barındıran sunucu bir rol oynamıyor.
* Sunucudaki API 'Ler korunamaz.
* Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma

Bir Identity üçüncü taraf oturum açma sağlayıcısıyla yapılandırın. Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.

Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin. Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın

Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın. Buradan, üçüncü taraf API kaynaklarını doğrudan Identity istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın.

Bu yaklaşımı önermiyoruz. Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir. OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur. Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.

* Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.
* Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın

İstemciden sunucu API 'sine bir API çağrısı yapın. Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.

Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:

* Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.
* Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.
