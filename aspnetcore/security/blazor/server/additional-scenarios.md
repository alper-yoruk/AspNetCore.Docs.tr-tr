---
Başlık: ' ASP.NET Core Blazor sunucu ek güvenlik senaryoları ' Yazar: Açıklama: ' daha Blazor fazla güvenlik senaryosu için sunucu yapılandırmayı öğrenin. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor sunucusu ek güvenlik senaryoları

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Belirteçleri bir Blazor sunucu uygulamasına geçirme

RazorBir sunucu uygulamasındaki bileşenlerin dışında bulunan belirteçler, Blazor Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir. Örnek kod için, tam bir örnek de dahil olmak üzere `Startup.ConfigureServices` [belirteçleri sunucu tarafı Blazor uygulamasına geçirme](https://github.com/javiercn/blazor-server-aad-sample)konusuna bakın.

BlazorSunucu uygulamasının kimliğini düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın Razor . Belirteçleri sağlama ve kimlik doğrulama tanımlama bilgisine kaydetme. Örneğin:

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

Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

**scoped** Blazor [Bağımlılık ekleme (dı)](xref:blazor/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

' De `Startup.ConfigureServices` , için hizmetler ekleyin:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host. cshtml* dosyasında, oluşturun ve örneğini `InitialApplicationState` uygulamaya bir parametre olarak geçirin:

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

`App`Bileşeninde (*app. Razor*), hizmeti çözümleyin ve bu parametreyi parametresindeki verilerle başlatın:

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

Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API 'YI çağırmak için belirteci alın:

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Açık KIMLIK Connect (OıDC) v 2.0 uç noktalarını kullan

Kimlik doğrulama kitaplığı ve Blazor şablonları Open ID Connect (OıDC) v 1.0 uç noktalarını kullanır. Bir v 2.0 uç noktası kullanmak için, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> ' deki seçeneğini yapılandırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Alternatif olarak, ayar uygulama ayarları (*appSettings. JSON*) dosyasında yapılabilir:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın. <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>Uygulama ayarları dosyasındaki ya da özelliğini <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> anahtarıyla ayarlayın.

### <a name="code-changes"></a>Kod değişiklikleri

* KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir. Daha fazla bilgi için bkz. [neden Microsoft Identity platform (v 2.0) güncelleştirmesi?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) Azure belgelerine bakın.
* Kaynaklar, v 2.0 uç noktaları için kapsam URI 'Lerinde belirtildiğinden, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> içindeki özellik ayarını kaldırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

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

OıDC sağlayıcısı uygulama kaydı açıklamasında kullanılacak uygulama KIMLIĞI URI 'sini bulabilirsiniz.
