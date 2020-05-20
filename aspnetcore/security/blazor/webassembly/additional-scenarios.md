---
<span data-ttu-id="2123b-101">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-103">'Blazor'</span></span>
- <span data-ttu-id="2123b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-104">'Identity'</span></span>
- <span data-ttu-id="2123b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-106">'Razor'</span></span>
- <span data-ttu-id="2123b-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="2123b-108">ASP.NET Core Blazor weelsembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="2123b-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="2123b-109">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="2123b-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="2123b-110">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="2123b-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="2123b-111">`AuthorizationMessageHandler`Hizmeti, `HttpClient` giden isteklere erişim belirteçleri eklemek için ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="2123b-112">Belirteçler, mevcut hizmet kullanılarak alınır `IAccessTokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="2123b-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="2123b-113">Bir belirteç alınamadığından, bir oluşturulur `AccessTokenNotAvailableException` .</span><span class="sxs-lookup"><span data-stu-id="2123b-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="2123b-114">`AccessTokenNotAvailableException``Redirect`, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2123b-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="2123b-115">, `AuthorizationMessageHandler` Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir `ConfigureHandler` .</span><span class="sxs-lookup"><span data-stu-id="2123b-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="2123b-116">Aşağıdaki örnekte, `AuthorizationMessageHandler` `HttpClient` içinde bir `Program.Main` (*program.cs*) yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="2123b-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

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

<span data-ttu-id="2123b-117">Kolaylık olması için, bir `BaseAddressAuthorizationMessageHandler` yetkılı URL olarak uygulama temel adresiyle önceden yapılandırılmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="2123b-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="2123b-118">Kimlik doğrulaması etkinleştirilmiş Blazor webassembly şablonları artık <xref:System.Net.Http.IHttpClientFactory> sunucu API 'si projesinde kullanarak bir <xref:System.Net.Http.HttpClient> ile birlikte ayarlanır `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="2123b-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="2123b-119">Önceki örnekte istemci oluşturulduğu yerde `CreateClient` , <xref:System.Net.Http.HttpClient> sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2123b-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="2123b-120"><xref:System.Net.Http.HttpClient>Daha sonra, yapılandırılmış bir basit model kullanarak yetkili istekler oluşturmak için kullanılır `try-catch` .</span><span class="sxs-lookup"><span data-stu-id="2123b-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="2123b-121">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2123b-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="2123b-122">Yazılan HttpClient</span><span class="sxs-lookup"><span data-stu-id="2123b-122">Typed HttpClient</span></span>

<span data-ttu-id="2123b-123">Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="2123b-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="2123b-124">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="2123b-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2123b-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="2123b-126">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2123b-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="2123b-127">HttpClient işleyicisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2123b-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="2123b-128">İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="2123b-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2123b-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="2123b-130">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="2123b-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="2123b-131">BlazorWeelsembly uygulaması genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama, adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="2123b-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="2123b-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2123b-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="2123b-133">Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="2123b-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="2123b-134">Bir bileşen <xref:System.Net.Http.HttpClient> , <xref:System.Net.Http.IHttpClientFactory> kimliği doğrulanmamış veya yetkisiz istekler yapmak için öğesinden öğesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2123b-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="2123b-135">Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="2123b-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="2123b-136">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="2123b-136">Request additional access tokens</span></span>

<span data-ttu-id="2123b-137">Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="2123b-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="2123b-138">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="2123b-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="2123b-139">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, Istemci uygulamasında ek kapsamlar yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2123b-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="2123b-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2123b-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="2123b-141">`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="2123b-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="2123b-142">Bir Razor bileşende:</span><span class="sxs-lookup"><span data-stu-id="2123b-142">In a Razor component:</span></span>

```razor
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

<span data-ttu-id="2123b-143">`TryGetToken`döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="2123b-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="2123b-144">`true`' i `token` kullanın.</span><span class="sxs-lookup"><span data-stu-id="2123b-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="2123b-145">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="2123b-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="2123b-146">API istek seçeneklerini getiren HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="2123b-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="2123b-147">Weelsembly uygulamasında WebAssembly üzerinde çalışırken Blazor , [HttpClient](xref:fundamentals/http-requests) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="2123b-148">Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2123b-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="2123b-149">Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="2123b-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="2123b-150">.NET WebAssembly 'ın uygulanması, `HttpClient` [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır.</span><span class="sxs-lookup"><span data-stu-id="2123b-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="2123b-151">Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2123b-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="2123b-152">HTTP getirme isteği seçenekleri `HttpRequestMessage` , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="2123b-153">`HttpRequestMessage`genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="2123b-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="2123b-154">Fetch isteği özelliği</span><span class="sxs-lookup"><span data-stu-id="2123b-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="2123b-155">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-156">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-157">'Blazor'</span></span>
- <span data-ttu-id="2123b-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-158">'Identity'</span></span>
- <span data-ttu-id="2123b-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-160">'Razor'</span></span>
- <span data-ttu-id="2123b-161">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-162">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-163">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-164">'Blazor'</span></span>
- <span data-ttu-id="2123b-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-165">'Identity'</span></span>
- <span data-ttu-id="2123b-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-167">'Razor'</span></span>
- <span data-ttu-id="2123b-168">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-169">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-170">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-171">'Blazor'</span></span>
- <span data-ttu-id="2123b-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-172">'Identity'</span></span>
- <span data-ttu-id="2123b-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-174">'Razor'</span></span>
- <span data-ttu-id="2123b-175">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-176">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-178">'Blazor'</span></span>
- <span data-ttu-id="2123b-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-179">'Identity'</span></span>
- <span data-ttu-id="2123b-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-181">'Razor'</span></span>
- <span data-ttu-id="2123b-182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-183">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-185">'Blazor'</span></span>
- <span data-ttu-id="2123b-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-186">'Identity'</span></span>
- <span data-ttu-id="2123b-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-188">'Razor'</span></span>
- <span data-ttu-id="2123b-189">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-190">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-192">'Blazor'</span></span>
- <span data-ttu-id="2123b-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-193">'Identity'</span></span>
- <span data-ttu-id="2123b-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-195">'Razor'</span></span>
- <span data-ttu-id="2123b-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-197">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-199">'Blazor'</span></span>
- <span data-ttu-id="2123b-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-200">'Identity'</span></span>
- <span data-ttu-id="2123b-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-202">'Razor'</span></span>
- <span data-ttu-id="2123b-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-204">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-206">'Blazor'</span></span>
- <span data-ttu-id="2123b-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-207">'Identity'</span></span>
- <span data-ttu-id="2123b-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-209">'Razor'</span></span>
- <span data-ttu-id="2123b-210">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-211">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-213">'Blazor'</span></span>
- <span data-ttu-id="2123b-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-214">'Identity'</span></span>
- <span data-ttu-id="2123b-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-216">'Razor'</span></span>
- <span data-ttu-id="2123b-217">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-218">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-220">'Blazor'</span></span>
- <span data-ttu-id="2123b-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-221">'Identity'</span></span>
- <span data-ttu-id="2123b-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-223">'Razor'</span></span>
- <span data-ttu-id="2123b-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-225">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-227">'Blazor'</span></span>
- <span data-ttu-id="2123b-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-228">'Identity'</span></span>
- <span data-ttu-id="2123b-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-230">'Razor'</span></span>
- <span data-ttu-id="2123b-231">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-232">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-234">'Blazor'</span></span>
- <span data-ttu-id="2123b-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-235">'Identity'</span></span>
- <span data-ttu-id="2123b-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-237">'Razor'</span></span>
- <span data-ttu-id="2123b-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-239">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-241">'Blazor'</span></span>
- <span data-ttu-id="2123b-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-242">'Identity'</span></span>
- <span data-ttu-id="2123b-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-244">'Razor'</span></span>
- <span data-ttu-id="2123b-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-246">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-248">'Blazor'</span></span>
- <span data-ttu-id="2123b-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-249">'Identity'</span></span>
- <span data-ttu-id="2123b-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-251">'Razor'</span></span>
- <span data-ttu-id="2123b-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-253">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-255">'Blazor'</span></span>
- <span data-ttu-id="2123b-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-256">'Identity'</span></span>
- <span data-ttu-id="2123b-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-258">'Razor'</span></span>
- <span data-ttu-id="2123b-259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-260">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-262">'Blazor'</span></span>
- <span data-ttu-id="2123b-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-263">'Identity'</span></span>
- <span data-ttu-id="2123b-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-265">'Razor'</span></span>
- <span data-ttu-id="2123b-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-266">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-267">------------------- | ---başlığı: ' ASP.NET Core Blazor webassembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-269">'Blazor'</span></span>
- <span data-ttu-id="2123b-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-270">'Identity'</span></span>
- <span data-ttu-id="2123b-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-272">'Razor'</span></span>
- <span data-ttu-id="2123b-273">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-274">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-276">'Blazor'</span></span>
- <span data-ttu-id="2123b-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-277">'Identity'</span></span>
- <span data-ttu-id="2123b-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-279">'Razor'</span></span>
- <span data-ttu-id="2123b-280">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-281">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-283">'Blazor'</span></span>
- <span data-ttu-id="2123b-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-284">'Identity'</span></span>
- <span data-ttu-id="2123b-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-286">'Razor'</span></span>
- <span data-ttu-id="2123b-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-288">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-290">'Blazor'</span></span>
- <span data-ttu-id="2123b-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-291">'Identity'</span></span>
- <span data-ttu-id="2123b-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-293">'Razor'</span></span>
- <span data-ttu-id="2123b-294">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-295">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-297">'Blazor'</span></span>
- <span data-ttu-id="2123b-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-298">'Identity'</span></span>
- <span data-ttu-id="2123b-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-300">'Razor'</span></span>
- <span data-ttu-id="2123b-301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-302">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-304">'Blazor'</span></span>
- <span data-ttu-id="2123b-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-305">'Identity'</span></span>
- <span data-ttu-id="2123b-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-307">'Razor'</span></span>
- <span data-ttu-id="2123b-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-309">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-310">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-311">'Blazor'</span></span>
- <span data-ttu-id="2123b-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-312">'Identity'</span></span>
- <span data-ttu-id="2123b-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-314">'Razor'</span></span>
- <span data-ttu-id="2123b-315">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-316">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-317">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-318">'Blazor'</span></span>
- <span data-ttu-id="2123b-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-319">'Identity'</span></span>
- <span data-ttu-id="2123b-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-321">'Razor'</span></span>
- <span data-ttu-id="2123b-322">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-323">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-324">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-325">'Blazor'</span></span>
- <span data-ttu-id="2123b-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-326">'Identity'</span></span>
- <span data-ttu-id="2123b-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-328">'Razor'</span></span>
- <span data-ttu-id="2123b-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-329">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-330">----------- | | `SetBrowserRequestCredentials`         |  [kimlik bilgileri](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`               |  [önbellek](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [mod](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [bütünlük](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="2123b-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="2123b-331">Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz `SetBrowserRequestOption` .</span><span class="sxs-lookup"><span data-stu-id="2123b-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="2123b-332">HTTP yanıtı, Blazor Yanıt içeriğindeki eşitleme okuma desteğini etkinleştirmek için genellikle webassembly uygulamasında arabelleğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="2123b-333">Yanıt akışı desteğini etkinleştirmek için `SetBrowserResponseStreamingEnabled` istekteki genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2123b-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="2123b-334">Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, `SetBrowserRequestCredentials` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="2123b-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="2123b-335">API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="2123b-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="2123b-336">CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, bir `Authorization` üst BILGIYE CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2123b-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="2123b-337">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="2123b-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="2123b-338">İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="2123b-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="2123b-339">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="2123b-339">Any method (verb).</span></span>
* <span data-ttu-id="2123b-340">`Content-Type`ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="2123b-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="2123b-341">Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="2123b-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="2123b-342">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).</span><span class="sxs-lookup"><span data-stu-id="2123b-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="2123b-343">Daha fazla bilgi için, bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni (*Bileşenler/httprequesttester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="2123b-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="2123b-344">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="2123b-344">Handle token request errors</span></span>

<span data-ttu-id="2123b-345">Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve Identity sağlayıcıda (IP), kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="2123b-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="2123b-346">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2123b-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="2123b-347">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="2123b-348">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="2123b-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="2123b-349">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="2123b-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="2123b-350">Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="2123b-351">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="2123b-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="2123b-352">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="2123b-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="2123b-353">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="2123b-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="2123b-354">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="2123b-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="2123b-355">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="2123b-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="2123b-356">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="2123b-357">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="2123b-358">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="2123b-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="2123b-359">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="2123b-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="2123b-360">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2123b-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="2123b-361">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="2123b-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="2123b-362">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="2123b-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="2123b-363">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="2123b-363">Store the current page state in session storage.</span></span> <span data-ttu-id="2123b-364">Sırasında `OnInitializeAsync` , devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="2123b-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="2123b-365">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="2123b-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="2123b-366">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2123b-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="2123b-367">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2123b-367">The following example shows how to:</span></span>

* <span data-ttu-id="2123b-368">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="2123b-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="2123b-369">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="2123b-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="2123b-370">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="2123b-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="2123b-371">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="2123b-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="2123b-372">Durum kapsayıcısı gibi bir şey kullandığınızda ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="2123b-373">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2123b-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="2123b-374">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2123b-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="2123b-375">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2123b-375">Customize app routes</span></span>

<span data-ttu-id="2123b-376">Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2123b-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="2123b-377">Yol</span><span class="sxs-lookup"><span data-stu-id="2123b-377">Route</span></span>                            | <span data-ttu-id="2123b-378">Amaç</span><span class="sxs-lookup"><span data-stu-id="2123b-378">Purpose</span></span> |
| ---
<span data-ttu-id="2123b-379">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-380">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-381">'Blazor'</span></span>
- <span data-ttu-id="2123b-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-382">'Identity'</span></span>
- <span data-ttu-id="2123b-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-384">'Razor'</span></span>
- <span data-ttu-id="2123b-385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-386">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-387">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-388">'Blazor'</span></span>
- <span data-ttu-id="2123b-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-389">'Identity'</span></span>
- <span data-ttu-id="2123b-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-391">'Razor'</span></span>
- <span data-ttu-id="2123b-392">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-393">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-394">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-395">'Blazor'</span></span>
- <span data-ttu-id="2123b-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-396">'Identity'</span></span>
- <span data-ttu-id="2123b-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-398">'Razor'</span></span>
- <span data-ttu-id="2123b-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-400">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-401">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-402">'Blazor'</span></span>
- <span data-ttu-id="2123b-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-403">'Identity'</span></span>
- <span data-ttu-id="2123b-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-405">'Razor'</span></span>
- <span data-ttu-id="2123b-406">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-407">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-408">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-409">'Blazor'</span></span>
- <span data-ttu-id="2123b-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-410">'Identity'</span></span>
- <span data-ttu-id="2123b-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-412">'Razor'</span></span>
- <span data-ttu-id="2123b-413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-414">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-415">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-416">'Blazor'</span></span>
- <span data-ttu-id="2123b-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-417">'Identity'</span></span>
- <span data-ttu-id="2123b-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-419">'Razor'</span></span>
- <span data-ttu-id="2123b-420">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-421">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-422">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-423">'Blazor'</span></span>
- <span data-ttu-id="2123b-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-424">'Identity'</span></span>
- <span data-ttu-id="2123b-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-426">'Razor'</span></span>
- <span data-ttu-id="2123b-427">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-428">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-429">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-430">'Blazor'</span></span>
- <span data-ttu-id="2123b-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-431">'Identity'</span></span>
- <span data-ttu-id="2123b-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-433">'Razor'</span></span>
- <span data-ttu-id="2123b-434">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-435">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-436">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-437">'Blazor'</span></span>
- <span data-ttu-id="2123b-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-438">'Identity'</span></span>
- <span data-ttu-id="2123b-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-440">'Razor'</span></span>
- <span data-ttu-id="2123b-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-442">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-443">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-444">'Blazor'</span></span>
- <span data-ttu-id="2123b-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-445">'Identity'</span></span>
- <span data-ttu-id="2123b-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-447">'Razor'</span></span>
- <span data-ttu-id="2123b-448">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-449">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-450">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-451">'Blazor'</span></span>
- <span data-ttu-id="2123b-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-452">'Identity'</span></span>
- <span data-ttu-id="2123b-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-454">'Razor'</span></span>
- <span data-ttu-id="2123b-455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-456">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-457">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-458">'Blazor'</span></span>
- <span data-ttu-id="2123b-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-459">'Identity'</span></span>
- <span data-ttu-id="2123b-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-461">'Razor'</span></span>
- <span data-ttu-id="2123b-462">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-463">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-464">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-465">'Blazor'</span></span>
- <span data-ttu-id="2123b-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-466">'Identity'</span></span>
- <span data-ttu-id="2123b-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-468">'Razor'</span></span>
- <span data-ttu-id="2123b-469">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-470">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-471">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-472">'Blazor'</span></span>
- <span data-ttu-id="2123b-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-473">'Identity'</span></span>
- <span data-ttu-id="2123b-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-475">'Razor'</span></span>
- <span data-ttu-id="2123b-476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-476">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-477">---------------- | ---başlığı: ' ASP.NET Core Blazor webassembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-478">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-479">'Blazor'</span></span>
- <span data-ttu-id="2123b-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-480">'Identity'</span></span>
- <span data-ttu-id="2123b-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-482">'Razor'</span></span>
- <span data-ttu-id="2123b-483">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-483">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-484">---- | | `authentication/login`           | Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="2123b-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="2123b-485">| | `authentication/login-callback`  | Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="2123b-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="2123b-486">| | `authentication/login-failed`    | Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2123b-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="2123b-487">| | `authentication/logout`          | Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="2123b-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="2123b-488">| | `authentication/logout-callback` | Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="2123b-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="2123b-489">| | `authentication/logout-failed`   | Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2123b-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="2123b-490">| | `authentication/logged-out`      | Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2123b-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="2123b-491">| | `authentication/profile`         | Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="2123b-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="2123b-492">| | `authentication/register`        | Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="2123b-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="2123b-493">Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="2123b-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="2123b-494">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2123b-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="2123b-495">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .</span><span class="sxs-lookup"><span data-stu-id="2123b-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="2123b-496">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2123b-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="2123b-497">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2123b-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="2123b-498">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini `RemoteAuthenticatorView` açık bir eylem parametresiyle işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="2123b-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="2123b-499">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="2123b-500">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2123b-500">Customize the authentication user interface</span></span>

<span data-ttu-id="2123b-501">`RemoteAuthenticatorView`Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="2123b-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="2123b-502">Her durum, özel olarak geçirerek özelleştirilebilir `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="2123b-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="2123b-503">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için `RemoteAuthenticatorView` aşağıdaki gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="2123b-504">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="2123b-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="2123b-505">, `RemoteAuthenticatorView` Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2123b-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="2123b-506">Yol</span><span class="sxs-lookup"><span data-stu-id="2123b-506">Route</span></span>                            | <span data-ttu-id="2123b-507">Parça</span><span class="sxs-lookup"><span data-stu-id="2123b-507">Fragment</span></span>                |
| ---
<span data-ttu-id="2123b-508">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-509">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-510">'Blazor'</span></span>
- <span data-ttu-id="2123b-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-511">'Identity'</span></span>
- <span data-ttu-id="2123b-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-513">'Razor'</span></span>
- <span data-ttu-id="2123b-514">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-515">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-516">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-517">'Blazor'</span></span>
- <span data-ttu-id="2123b-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-518">'Identity'</span></span>
- <span data-ttu-id="2123b-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-520">'Razor'</span></span>
- <span data-ttu-id="2123b-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-522">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-523">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-524">'Blazor'</span></span>
- <span data-ttu-id="2123b-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-525">'Identity'</span></span>
- <span data-ttu-id="2123b-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-527">'Razor'</span></span>
- <span data-ttu-id="2123b-528">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-529">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-530">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-531">'Blazor'</span></span>
- <span data-ttu-id="2123b-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-532">'Identity'</span></span>
- <span data-ttu-id="2123b-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-534">'Razor'</span></span>
- <span data-ttu-id="2123b-535">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-536">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-537">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-538">'Blazor'</span></span>
- <span data-ttu-id="2123b-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-539">'Identity'</span></span>
- <span data-ttu-id="2123b-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-541">'Razor'</span></span>
- <span data-ttu-id="2123b-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-543">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-544">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-545">'Blazor'</span></span>
- <span data-ttu-id="2123b-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-546">'Identity'</span></span>
- <span data-ttu-id="2123b-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-548">'Razor'</span></span>
- <span data-ttu-id="2123b-549">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-550">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-551">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-552">'Blazor'</span></span>
- <span data-ttu-id="2123b-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-553">'Identity'</span></span>
- <span data-ttu-id="2123b-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-555">'Razor'</span></span>
- <span data-ttu-id="2123b-556">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-557">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-558">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-559">'Blazor'</span></span>
- <span data-ttu-id="2123b-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-560">'Identity'</span></span>
- <span data-ttu-id="2123b-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-562">'Razor'</span></span>
- <span data-ttu-id="2123b-563">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-564">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-565">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-566">'Blazor'</span></span>
- <span data-ttu-id="2123b-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-567">'Identity'</span></span>
- <span data-ttu-id="2123b-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-569">'Razor'</span></span>
- <span data-ttu-id="2123b-570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-571">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-572">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-573">'Blazor'</span></span>
- <span data-ttu-id="2123b-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-574">'Identity'</span></span>
- <span data-ttu-id="2123b-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-576">'Razor'</span></span>
- <span data-ttu-id="2123b-577">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-578">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-579">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-580">'Blazor'</span></span>
- <span data-ttu-id="2123b-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-581">'Identity'</span></span>
- <span data-ttu-id="2123b-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-583">'Razor'</span></span>
- <span data-ttu-id="2123b-584">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-585">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-586">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-587">'Blazor'</span></span>
- <span data-ttu-id="2123b-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-588">'Identity'</span></span>
- <span data-ttu-id="2123b-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-590">'Razor'</span></span>
- <span data-ttu-id="2123b-591">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-592">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-593">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-594">'Blazor'</span></span>
- <span data-ttu-id="2123b-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-595">'Identity'</span></span>
- <span data-ttu-id="2123b-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-597">'Razor'</span></span>
- <span data-ttu-id="2123b-598">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-599">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-600">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-601">'Blazor'</span></span>
- <span data-ttu-id="2123b-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-602">'Identity'</span></span>
- <span data-ttu-id="2123b-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-604">'Razor'</span></span>
- <span data-ttu-id="2123b-605">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-605">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-606">---------------- | ---başlığı: ' ASP.NET Core Blazor webassembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-607">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-608">'Blazor'</span></span>
- <span data-ttu-id="2123b-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-609">'Identity'</span></span>
- <span data-ttu-id="2123b-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-611">'Razor'</span></span>
- <span data-ttu-id="2123b-612">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-613">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-614">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-615">'Blazor'</span></span>
- <span data-ttu-id="2123b-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-616">'Identity'</span></span>
- <span data-ttu-id="2123b-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-618">'Razor'</span></span>
- <span data-ttu-id="2123b-619">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-620">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-621">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-622">'Blazor'</span></span>
- <span data-ttu-id="2123b-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-623">'Identity'</span></span>
- <span data-ttu-id="2123b-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-625">'Razor'</span></span>
- <span data-ttu-id="2123b-626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-627">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-628">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-629">'Blazor'</span></span>
- <span data-ttu-id="2123b-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-630">'Identity'</span></span>
- <span data-ttu-id="2123b-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-632">'Razor'</span></span>
- <span data-ttu-id="2123b-633">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-634">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-635">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-636">'Blazor'</span></span>
- <span data-ttu-id="2123b-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-637">'Identity'</span></span>
- <span data-ttu-id="2123b-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-639">'Razor'</span></span>
- <span data-ttu-id="2123b-640">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-641">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-642">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-643">'Blazor'</span></span>
- <span data-ttu-id="2123b-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-644">'Identity'</span></span>
- <span data-ttu-id="2123b-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-646">'Razor'</span></span>
- <span data-ttu-id="2123b-647">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-648">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-649">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-650">'Blazor'</span></span>
- <span data-ttu-id="2123b-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-651">'Identity'</span></span>
- <span data-ttu-id="2123b-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-653">'Razor'</span></span>
- <span data-ttu-id="2123b-654">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-655">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-656">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-657">'Blazor'</span></span>
- <span data-ttu-id="2123b-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-658">'Identity'</span></span>
- <span data-ttu-id="2123b-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-660">'Razor'</span></span>
- <span data-ttu-id="2123b-661">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2123b-662">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="2123b-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="2123b-663">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2123b-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2123b-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2123b-664">'Blazor'</span></span>
- <span data-ttu-id="2123b-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2123b-665">'Identity'</span></span>
- <span data-ttu-id="2123b-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2123b-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="2123b-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2123b-667">'Razor'</span></span>
- <span data-ttu-id="2123b-668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2123b-668">'SignalR' uid:</span></span> 

<span data-ttu-id="2123b-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="2123b-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="2123b-670">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2123b-670">Customize the user</span></span>

<span data-ttu-id="2123b-671">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="2123b-672">Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="2123b-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="2123b-673">Sınıfı genişleten bir sınıf oluşturun `RemoteUserAccount` :</span><span class="sxs-lookup"><span data-stu-id="2123b-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="2123b-674">Şunu genişleten bir fabrika oluşturun `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="2123b-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="2123b-675">`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun.</span><span class="sxs-lookup"><span data-stu-id="2123b-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="2123b-676">Aşağıdaki kayıtlardan herhangi biri geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="2123b-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="2123b-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2123b-677">`AddOidcAuthentication`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="2123b-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2123b-678">`AddMsalAuthentication`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="2123b-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="2123b-679">`AddApiAuthorization`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="2123b-680">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="2123b-680">Support prerendering with authentication</span></span>

<span data-ttu-id="2123b-681">Barındırılan Blazor webassembly uygulama konularından birindeki yönergeleri uyguladıktan sonra aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2123b-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="2123b-682">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="2123b-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="2123b-683">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="2123b-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="2123b-684">Istemci uygulamasının `Program` sınıfında (*program.cs*), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin,) göre çarpanlara ayırın `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="2123b-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="2123b-685">Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="2123b-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="2123b-686">Sunucu uygulamasının `Startup.Configure` yönteminde, `endpoints.MapFallbackToFile("index.html")` ile değiştirin `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="2123b-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="2123b-687">Sunucu uygulamasında, yoksa bir *sayfa* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2123b-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="2123b-688">Sunucu uygulamasının *Sayfalar* klasörünün içinde bir *_Host. cshtml* sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2123b-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="2123b-689">İçeriği Istemci uygulamasının *Wwwroot/index.html* dosyasından *Sayfalar/_Host. cshtml* dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="2123b-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="2123b-690">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2123b-690">Update the file's contents:</span></span>

* <span data-ttu-id="2123b-691">`@page "_Host"`Dosyanın en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2123b-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="2123b-692">`<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2123b-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="2123b-693">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="2123b-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="2123b-694">Bir Blazor üçüncü taraf sağlayıcı ile barındırılan webassembly uygulamasının kimliğini doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="2123b-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="2123b-695">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2123b-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="2123b-696">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="2123b-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="2123b-697">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="2123b-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="2123b-698">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="2123b-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="2123b-699">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="2123b-699">In this scenario:</span></span>

* <span data-ttu-id="2123b-700">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="2123b-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="2123b-701">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="2123b-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="2123b-702">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="2123b-703">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="2123b-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="2123b-704">IdentityBir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2123b-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="2123b-705">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="2123b-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="2123b-706">Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin.</span><span class="sxs-lookup"><span data-stu-id="2123b-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="2123b-707">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="2123b-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="2123b-708">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="2123b-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="2123b-709">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2123b-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="2123b-710">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın Identity .</span><span class="sxs-lookup"><span data-stu-id="2123b-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="2123b-711">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="2123b-711">We don't recommend this approach.</span></span> <span data-ttu-id="2123b-712">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2123b-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="2123b-713">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2123b-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="2123b-714">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="2123b-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="2123b-715">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="2123b-716">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="2123b-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="2123b-717">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="2123b-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="2123b-718">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2123b-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="2123b-719">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="2123b-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="2123b-720">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="2123b-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="2123b-721">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="2123b-722">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="2123b-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="2123b-723">Açık KIMLIK Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="2123b-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="2123b-724">Kimlik doğrulama kitaplığı ve Blazor şablonları Open ID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="2123b-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="2123b-725">Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2123b-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="2123b-726">Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` `Authority` :</span><span class="sxs-lookup"><span data-stu-id="2123b-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="2123b-727">Alternatif olarak, ayar uygulama ayarları (*appSettings. JSON*) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="2123b-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="2123b-728">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, `Authority` özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2123b-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="2123b-729">`JwtBearerOptions`Uygulama ayarları dosyasındaki ya da özelliğini `Authority` anahtarıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2123b-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="2123b-730">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="2123b-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="2123b-731">Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="2123b-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
