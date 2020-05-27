---
<span data-ttu-id="4b640-101">Başlık: ' ASP.NET Core Blazor sunucu ek güvenlik senaryoları ' Yazar: Açıklama: ' daha Blazor fazla güvenlik senaryosu için sunucu yapılandırmayı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="4b640-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="4b640-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4b640-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b640-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b640-103">'Blazor'</span></span>
- <span data-ttu-id="4b640-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b640-104">'Identity'</span></span>
- <span data-ttu-id="4b640-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b640-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b640-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b640-106">'Razor'</span></span>
- <span data-ttu-id="4b640-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4b640-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="4b640-108">ASP.NET Core Blazor sunucusu ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="4b640-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="4b640-109">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="4b640-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="4b640-110">Belirteçleri bir Blazor sunucu uygulamasına geçirme</span><span class="sxs-lookup"><span data-stu-id="4b640-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="4b640-111">RazorBir sunucu uygulamasındaki bileşenlerin dışında bulunan belirteçler, Blazor Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4b640-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="4b640-112">Örnek kod için, tam bir örnek de dahil olmak üzere `Startup.ConfigureServices` [belirteçleri sunucu tarafı Blazor uygulamasına geçirme](https://github.com/javiercn/blazor-server-aad-sample)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="4b640-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="4b640-113">BlazorSunucu uygulamasının kimliğini düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın Razor .</span><span class="sxs-lookup"><span data-stu-id="4b640-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="4b640-114">Belirteçleri sağlama ve kimlik doğrulama tanımlama bilgisine kaydetme.</span><span class="sxs-lookup"><span data-stu-id="4b640-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="4b640-115">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4b640-115">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="4b640-116">Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="4b640-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4b640-117">**scoped** Blazor [Bağımlılık ekleme (dı)](xref:blazor/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="4b640-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4b640-118">' De `Startup.ConfigureServices` , için hizmetler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4b640-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4b640-119">*_Host. cshtml* dosyasında, oluşturun ve örneğini `InitialApplicationState` uygulamaya bir parametre olarak geçirin:</span><span class="sxs-lookup"><span data-stu-id="4b640-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="4b640-120">`App`Bileşeninde (*app. Razor*), hizmeti çözümleyin ve bu parametreyi parametresindeki verilerle başlatın:</span><span class="sxs-lookup"><span data-stu-id="4b640-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="4b640-121">Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API 'YI çağırmak için belirteci alın:</span><span class="sxs-lookup"><span data-stu-id="4b640-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="4b640-122">Açık KIMLIK Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="4b640-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="4b640-123">Kimlik doğrulama kitaplığı ve Blazor şablonları Open ID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="4b640-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="4b640-124">Bir v 2.0 uç noktası kullanmak için, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> ' deki seçeneğini yapılandırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4b640-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="4b640-125">Alternatif olarak, ayar uygulama ayarları (*appSettings. JSON*) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="4b640-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="4b640-126">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4b640-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="4b640-127"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>Uygulama ayarları dosyasındaki ya da özelliğini <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> anahtarıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4b640-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="4b640-128">Kod değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="4b640-128">Code changes</span></span>

* <span data-ttu-id="4b640-129">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="4b640-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="4b640-130">Daha fazla bilgi için bkz. [neden Microsoft Identity platform (v 2.0) güncelleştirmesi?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="4b640-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="4b640-131">Azure belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="4b640-131">in the Azure documentation.</span></span>
* <span data-ttu-id="4b640-132">Kaynaklar, v 2.0 uç noktaları için kapsam URI 'Lerinde belirtildiğinden, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> içindeki özellik ayarını kaldırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4b640-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="4b640-133">OıDC sağlayıcısı uygulama kaydı açıklamasında kullanılacak uygulama KIMLIĞI URI 'sini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4b640-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
