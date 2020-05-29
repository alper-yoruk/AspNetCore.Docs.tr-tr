---
<span data-ttu-id="e7c5e-101">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-103">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-104">'Identity'</span></span>
- <span data-ttu-id="e7c5e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-106">'Razor'</span></span>
- <span data-ttu-id="e7c5e-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="e7c5e-108">ASP.NET Core Blazor weelsembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="e7c5e-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="e7c5e-109">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e7c5e-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="e7c5e-110">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="e7c5e-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="e7c5e-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Hizmeti, <xref:System.Net.Http.HttpClient> giden isteklere erişim belirteçleri eklemek için ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="e7c5e-112">Belirteçler, mevcut hizmet kullanılarak alınır <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="e7c5e-113">Bir belirteç alınamadığından, bir oluşturulur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="e7c5e-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="e7c5e-115">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="e7c5e-116">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> içinde bir `Program.Main` (*program.cs*) yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="e7c5e-117">Kolaylık olması için, bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> yetkılı URL olarak uygulama temel adresiyle önceden yapılandırılmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="e7c5e-118">Kimlik doğrulaması etkinleştirilmiş Blazor webassembly şablonları artık <xref:System.Net.Http.IHttpClientFactory> sunucu API 'si projesinde kullanarak bir <xref:System.Net.Http.HttpClient> ile birlikte ayarlanır <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="e7c5e-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="e7c5e-119">Önceki örnekte istemci oluşturulduğu yerde <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> , <xref:System.Net.Http.HttpClient> sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="e7c5e-120"><xref:System.Net.Http.HttpClient>Daha sonra, yapılandırılmış bir basit [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deseninin kullanıldığı yetkili istekleri yapmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="e7c5e-121">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="e7c5e-122">Yazılan HttpClient</span><span class="sxs-lookup"><span data-stu-id="e7c5e-122">Typed HttpClient</span></span>

<span data-ttu-id="e7c5e-123">Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="e7c5e-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="e7c5e-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="e7c5e-126">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="e7c5e-127">HttpClient işleyicisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7c5e-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="e7c5e-128">İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="e7c5e-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="e7c5e-130">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="e7c5e-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="e7c5e-131">BlazorWeelsembly uygulaması genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama, adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="e7c5e-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="e7c5e-133">Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="e7c5e-134">Bir bileşen <xref:System.Net.Http.HttpClient> , <xref:System.Net.Http.IHttpClientFactory> kimliği doğrulanmamış veya yetkisiz istekler yapmak için öğesinden öğesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="e7c5e-135">Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="e7c5e-136">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="e7c5e-136">Request additional access tokens</span></span>

<span data-ttu-id="e7c5e-137">Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="e7c5e-138">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="e7c5e-139">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, Istemci uygulamasında ek kapsamlar yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="e7c5e-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="e7c5e-141">`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="e7c5e-142">Bir Razor bileşende:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-142">In a Razor component:</span></span>

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

<span data-ttu-id="e7c5e-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="e7c5e-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="e7c5e-144">`true`' i `token` kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="e7c5e-145">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="e7c5e-146">API istek seçeneklerini getiren HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="e7c5e-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="e7c5e-147">Weelsembly uygulamasında WebAssembly üzerinde çalışırken Blazor , [HttpClient](xref:fundamentals/http-requests) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="e7c5e-148">Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="e7c5e-149">Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="e7c5e-150">.NET WebAssembly 'ın uygulanması, <xref:System.Net.Http.HttpClient> [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="e7c5e-151">Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="e7c5e-152">HTTP getirme isteği seçenekleri <xref:System.Net.Http.HttpRequestMessage> , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="e7c5e-153">Genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="e7c5e-153">Extension method</span></span> | <span data-ttu-id="e7c5e-154">Fetch isteği özelliği</span><span class="sxs-lookup"><span data-stu-id="e7c5e-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="e7c5e-155">Credentials</span><span class="sxs-lookup"><span data-stu-id="e7c5e-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="e7c5e-156">önbellek</span><span class="sxs-lookup"><span data-stu-id="e7c5e-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="e7c5e-157">modundaysa</span><span class="sxs-lookup"><span data-stu-id="e7c5e-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="e7c5e-158">doğruluğunu</span><span class="sxs-lookup"><span data-stu-id="e7c5e-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="e7c5e-159">Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="e7c5e-160">HTTP yanıtı, Blazor Yanıt içeriğindeki eşitleme okuma desteğini etkinleştirmek için genellikle webassembly uygulamasında arabelleğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="e7c5e-161">Yanıt akışı desteğini etkinleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> istekteki genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="e7c5e-162">Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="e7c5e-163">API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="e7c5e-164">CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, bir `Authorization` üst BILGIYE CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="e7c5e-165">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="e7c5e-166">İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="e7c5e-167">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-167">Any method (verb).</span></span>
* <span data-ttu-id="e7c5e-168">`Content-Type`ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="e7c5e-169">Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="e7c5e-170">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="e7c5e-171">Daha fazla bilgi için, bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni (*Bileşenler/httprequesttester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="e7c5e-172">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="e7c5e-172">Handle token request errors</span></span>

<span data-ttu-id="e7c5e-173">Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve Identity sağlayıcıda (IP), kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="e7c5e-174">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="e7c5e-175">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="e7c5e-176">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="e7c5e-177">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="e7c5e-178">Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="e7c5e-179">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="e7c5e-180">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="e7c5e-181">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="e7c5e-182">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="e7c5e-183">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="e7c5e-184">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="e7c5e-185">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="e7c5e-186">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="e7c5e-187">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="e7c5e-188">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="e7c5e-189">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="e7c5e-190">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="e7c5e-191">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-191">Store the current page state in session storage.</span></span> <span data-ttu-id="e7c5e-192">[Onınitializedadsync yaşam döngüsü olayı](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) sırasında, devam etmeden önce durumun geri yüklenip yüklenemeyeceğini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="e7c5e-193">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="e7c5e-194">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="e7c5e-195">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-195">The following example shows how to:</span></span>

* <span data-ttu-id="e7c5e-196">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="e7c5e-197">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="e7c5e-198">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="e7c5e-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="e7c5e-199">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="e7c5e-200">Durum kapsayıcısı gibi bir şey kullandığınızda ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="e7c5e-201">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="e7c5e-202">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="e7c5e-203">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e7c5e-203">Customize app routes</span></span>

<span data-ttu-id="e7c5e-204">Varsayılan olarak, [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) kitaplığı, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="e7c5e-205">Yol</span><span class="sxs-lookup"><span data-stu-id="e7c5e-205">Route</span></span>                            | <span data-ttu-id="e7c5e-206">Amaç</span><span class="sxs-lookup"><span data-stu-id="e7c5e-206">Purpose</span></span> |
| ---
<span data-ttu-id="e7c5e-207">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-209">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-210">'Identity'</span></span>
- <span data-ttu-id="e7c5e-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-212">'Razor'</span></span>
- <span data-ttu-id="e7c5e-213">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-214">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-215">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-216">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-217">'Identity'</span></span>
- <span data-ttu-id="e7c5e-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-219">'Razor'</span></span>
- <span data-ttu-id="e7c5e-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-221">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-222">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-223">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-224">'Identity'</span></span>
- <span data-ttu-id="e7c5e-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-226">'Razor'</span></span>
- <span data-ttu-id="e7c5e-227">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-228">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-229">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-230">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-231">'Identity'</span></span>
- <span data-ttu-id="e7c5e-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-233">'Razor'</span></span>
- <span data-ttu-id="e7c5e-234">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-235">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-236">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-237">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-238">'Identity'</span></span>
- <span data-ttu-id="e7c5e-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-240">'Razor'</span></span>
- <span data-ttu-id="e7c5e-241">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-242">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-243">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-244">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-245">'Identity'</span></span>
- <span data-ttu-id="e7c5e-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-247">'Razor'</span></span>
- <span data-ttu-id="e7c5e-248">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-249">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-250">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-251">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-252">'Identity'</span></span>
- <span data-ttu-id="e7c5e-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-254">'Razor'</span></span>
- <span data-ttu-id="e7c5e-255">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-256">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-257">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-258">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-259">'Identity'</span></span>
- <span data-ttu-id="e7c5e-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-261">'Razor'</span></span>
- <span data-ttu-id="e7c5e-262">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-263">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-264">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-265">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-266">'Identity'</span></span>
- <span data-ttu-id="e7c5e-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-268">'Razor'</span></span>
- <span data-ttu-id="e7c5e-269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-270">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-271">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-272">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-273">'Identity'</span></span>
- <span data-ttu-id="e7c5e-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-275">'Razor'</span></span>
- <span data-ttu-id="e7c5e-276">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-277">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-278">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-279">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-280">'Identity'</span></span>
- <span data-ttu-id="e7c5e-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-282">'Razor'</span></span>
- <span data-ttu-id="e7c5e-283">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-284">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-285">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-286">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-287">'Identity'</span></span>
- <span data-ttu-id="e7c5e-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-289">'Razor'</span></span>
- <span data-ttu-id="e7c5e-290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-291">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-292">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-293">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-294">'Identity'</span></span>
- <span data-ttu-id="e7c5e-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-296">'Razor'</span></span>
- <span data-ttu-id="e7c5e-297">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-298">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-299">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-300">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-301">'Identity'</span></span>
- <span data-ttu-id="e7c5e-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-303">'Razor'</span></span>
- <span data-ttu-id="e7c5e-304">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-304">'SignalR' uid:</span></span> 

<span data-ttu-id="e7c5e-305">---------------- | ---başlığı: ' ASP.NET Core Blazor webassembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-306">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-307">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-308">'Identity'</span></span>
- <span data-ttu-id="e7c5e-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-310">'Razor'</span></span>
- <span data-ttu-id="e7c5e-311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-311">'SignalR' uid:</span></span> 

<span data-ttu-id="e7c5e-312">---- | | `authentication/login`           | Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="e7c5e-313">| | `authentication/login-callback`  | Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="e7c5e-314">| | `authentication/login-failed`    | Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="e7c5e-315">| | `authentication/logout`          | Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="e7c5e-316">| | `authentication/logout-callback` | Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="e7c5e-317">| | `authentication/logout-failed`   | Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="e7c5e-318">| | `authentication/logged-out`      | Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="e7c5e-319">| | `authentication/profile`         | Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="e7c5e-320">| | `authentication/register`        | Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="e7c5e-321">Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e7c5e-322">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="e7c5e-323">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="e7c5e-324">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="e7c5e-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="e7c5e-326">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> açık bir eylem parametresiyle işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="e7c5e-327">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="e7c5e-328">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e7c5e-328">Customize the authentication user interface</span></span>

<span data-ttu-id="e7c5e-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="e7c5e-330">Her durum, özel olarak geçirerek özelleştirilebilir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="e7c5e-331">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> aşağıdaki gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="e7c5e-332">`Authentication`bileşen (*Sayfalar/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="e7c5e-333">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="e7c5e-334">Yol</span><span class="sxs-lookup"><span data-stu-id="e7c5e-334">Route</span></span>                            | <span data-ttu-id="e7c5e-335">Parça</span><span class="sxs-lookup"><span data-stu-id="e7c5e-335">Fragment</span></span>                |
| ---
<span data-ttu-id="e7c5e-336">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-337">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-338">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-339">'Identity'</span></span>
- <span data-ttu-id="e7c5e-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-341">'Razor'</span></span>
- <span data-ttu-id="e7c5e-342">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-343">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-344">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-345">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-346">'Identity'</span></span>
- <span data-ttu-id="e7c5e-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-348">'Razor'</span></span>
- <span data-ttu-id="e7c5e-349">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-350">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-351">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-352">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-353">'Identity'</span></span>
- <span data-ttu-id="e7c5e-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-355">'Razor'</span></span>
- <span data-ttu-id="e7c5e-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-357">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-358">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-359">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-360">'Identity'</span></span>
- <span data-ttu-id="e7c5e-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-362">'Razor'</span></span>
- <span data-ttu-id="e7c5e-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-364">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-365">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-366">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-367">'Identity'</span></span>
- <span data-ttu-id="e7c5e-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-369">'Razor'</span></span>
- <span data-ttu-id="e7c5e-370">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-371">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-372">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-373">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-374">'Identity'</span></span>
- <span data-ttu-id="e7c5e-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-376">'Razor'</span></span>
- <span data-ttu-id="e7c5e-377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-378">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-379">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-380">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-381">'Identity'</span></span>
- <span data-ttu-id="e7c5e-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-383">'Razor'</span></span>
- <span data-ttu-id="e7c5e-384">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-385">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-386">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-387">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-388">'Identity'</span></span>
- <span data-ttu-id="e7c5e-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-390">'Razor'</span></span>
- <span data-ttu-id="e7c5e-391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-392">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-393">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-394">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-395">'Identity'</span></span>
- <span data-ttu-id="e7c5e-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-397">'Razor'</span></span>
- <span data-ttu-id="e7c5e-398">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-399">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-400">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-401">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-402">'Identity'</span></span>
- <span data-ttu-id="e7c5e-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-404">'Razor'</span></span>
- <span data-ttu-id="e7c5e-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-406">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-407">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-408">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-409">'Identity'</span></span>
- <span data-ttu-id="e7c5e-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-411">'Razor'</span></span>
- <span data-ttu-id="e7c5e-412">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-413">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-414">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-415">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-416">'Identity'</span></span>
- <span data-ttu-id="e7c5e-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-418">'Razor'</span></span>
- <span data-ttu-id="e7c5e-419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-420">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-421">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-422">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-423">'Identity'</span></span>
- <span data-ttu-id="e7c5e-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-425">'Razor'</span></span>
- <span data-ttu-id="e7c5e-426">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-427">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-428">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-429">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-430">'Identity'</span></span>
- <span data-ttu-id="e7c5e-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-432">'Razor'</span></span>
- <span data-ttu-id="e7c5e-433">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-433">'SignalR' uid:</span></span> 

<span data-ttu-id="e7c5e-434">---------------- | ---başlığı: ' ASP.NET Core Blazor webassembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-435">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-436">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-437">'Identity'</span></span>
- <span data-ttu-id="e7c5e-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-439">'Razor'</span></span>
- <span data-ttu-id="e7c5e-440">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-441">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-442">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-443">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-444">'Identity'</span></span>
- <span data-ttu-id="e7c5e-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-446">'Razor'</span></span>
- <span data-ttu-id="e7c5e-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-448">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-449">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-450">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-451">'Identity'</span></span>
- <span data-ttu-id="e7c5e-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-453">'Razor'</span></span>
- <span data-ttu-id="e7c5e-454">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-455">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-456">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-457">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-458">'Identity'</span></span>
- <span data-ttu-id="e7c5e-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-460">'Razor'</span></span>
- <span data-ttu-id="e7c5e-461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-462">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-463">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-464">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-465">'Identity'</span></span>
- <span data-ttu-id="e7c5e-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-467">'Razor'</span></span>
- <span data-ttu-id="e7c5e-468">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-469">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-470">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-471">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-472">'Identity'</span></span>
- <span data-ttu-id="e7c5e-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-474">'Razor'</span></span>
- <span data-ttu-id="e7c5e-475">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-476">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-477">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-478">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-479">'Identity'</span></span>
- <span data-ttu-id="e7c5e-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-481">'Razor'</span></span>
- <span data-ttu-id="e7c5e-482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-483">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-484">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-485">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-486">'Identity'</span></span>
- <span data-ttu-id="e7c5e-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-488">'Razor'</span></span>
- <span data-ttu-id="e7c5e-489">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7c5e-490">Başlık: ' ASP.NET Core Blazor weelsembly ek güvenlik senaryoları ' Yazar: Açıklama: ' Blazor ek güvenlik senaryoları için webassembly 'ı nasıl yapılandıracağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="e7c5e-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="e7c5e-491">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7c5e-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-492">'Blazor'</span></span>
- <span data-ttu-id="e7c5e-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-493">'Identity'</span></span>
- <span data-ttu-id="e7c5e-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7c5e-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7c5e-495">'Razor'</span></span>
- <span data-ttu-id="e7c5e-496">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-496">'SignalR' uid:</span></span> 

<span data-ttu-id="e7c5e-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="e7c5e-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="e7c5e-498">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e7c5e-498">Customize the user</span></span>

<span data-ttu-id="e7c5e-499">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="e7c5e-500">Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="e7c5e-501">Sınıfı genişleten bir sınıf oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="e7c5e-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="e7c5e-502">Şunu genişleten bir fabrika oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="e7c5e-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="e7c5e-503">`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="e7c5e-504">Aşağıdaki kayıtlardan herhangi biri geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="e7c5e-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="e7c5e-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="e7c5e-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="e7c5e-508">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="e7c5e-508">Support prerendering with authentication</span></span>

<span data-ttu-id="e7c5e-509">Barındırılan Blazor webassembly uygulama konularından birindeki yönergeleri uyguladıktan sonra aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="e7c5e-510">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="e7c5e-511">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="e7c5e-512">Istemci uygulamasının `Program` sınıfında (*program.cs*), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin,) göre çarpanlara ayırın `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="e7c5e-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="e7c5e-513">Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="e7c5e-514">Sunucu uygulamasının `Startup.Configure` yönteminde, [uç noktaları değiştirin. Son noktalarla MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="e7c5e-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="e7c5e-515">Sunucu uygulamasında, yoksa bir *sayfa* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="e7c5e-516">Sunucu uygulamasının *Sayfalar* klasörünün içinde bir *_Host. cshtml* sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="e7c5e-517">İçeriği Istemci uygulamasının *Wwwroot/index.html* dosyasından *Sayfalar/_Host. cshtml* dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="e7c5e-518">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-518">Update the file's contents:</span></span>

* <span data-ttu-id="e7c5e-519">`@page "_Host"`Dosyanın en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="e7c5e-520">`<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="e7c5e-521">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="e7c5e-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="e7c5e-522">Bir Blazor üçüncü taraf sağlayıcı ile barındırılan webassembly uygulamasının kimliğini doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="e7c5e-523">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="e7c5e-524">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="e7c5e-525">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="e7c5e-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="e7c5e-526">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="e7c5e-527">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-527">In this scenario:</span></span>

* <span data-ttu-id="e7c5e-528">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="e7c5e-529">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="e7c5e-530">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="e7c5e-531">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="e7c5e-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="e7c5e-532">IdentityBir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="e7c5e-533">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="e7c5e-534">Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="e7c5e-535">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="e7c5e-536">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="e7c5e-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="e7c5e-537">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="e7c5e-538">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın Identity .</span><span class="sxs-lookup"><span data-stu-id="e7c5e-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="e7c5e-539">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-539">We don't recommend this approach.</span></span> <span data-ttu-id="e7c5e-540">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="e7c5e-541">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="e7c5e-542">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="e7c5e-543">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="e7c5e-544">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="e7c5e-545">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="e7c5e-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="e7c5e-546">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="e7c5e-547">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="e7c5e-548">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="e7c5e-549">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="e7c5e-550">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="e7c5e-551">Açık KIMLIK Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="e7c5e-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="e7c5e-552">Kimlik doğrulama kitaplığı ve Blazor şablonları Open ID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="e7c5e-553">Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="e7c5e-554">Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="e7c5e-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="e7c5e-555">Alternatif olarak, ayar uygulama ayarları (*appSettings. JSON*) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="e7c5e-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="e7c5e-556">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="e7c5e-557"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>Ya da uygulama ayarları dosyasındaki (*appSettings. JSON*) özelliğini `Authority` anahtarıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="e7c5e-558">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="e7c5e-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="e7c5e-559">Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="e7c5e-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
