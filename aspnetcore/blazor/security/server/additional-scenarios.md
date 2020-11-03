---
title: ASP.NET Core Blazor Server ek güvenlik senaryoları
author: guardrex
description: Blazor ServerEk güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234445"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server ek güvenlik senaryoları

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Belirteçleri bir uygulamaya geçirin Blazor Server</h2>

Bir uygulamadaki bileşenlerin dışında bulunan belirteçler, Razor Blazor Server Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.

Uygulamanın kimliğini Blazor Server düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın Razor . Belirteçleri sağlama ve kimlik doğrulamaya kaydetme cookie . Örneğin:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

İsteğe bağlı olarak, ek kapsamlar ile eklenir `options.Scope.Add("{SCOPE}");` , burada yer tutucu `{SCOPE}` Eklenecek ek kapsamdır.

**scoped** Blazor [Bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:

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

Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

`_Host.cshtml`Dosyasında, öğesini oluşturup örnek `InitialApplicationState` bir parametre olarak uygulamaya geçirin:

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

`App`Bileşeninde ( `App.razor` ), hizmeti çözümleyin ve parametresindeki verilerle başlatın:

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

NuGet paketi için uygulamaya bir paket başvurusu ekleyin [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .

Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API isteğinin belirtecini alın:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Kimlik doğrulama düzenini ayarlama</h2>

Birden fazla kimlik doğrulama ara yazılımı kullanan ve dolayısıyla birden fazla kimlik doğrulama şemasına sahip olan bir uygulama için, Blazor tarafından kullanılan şema, öğesinin uç nokta yapılandırmasında açıkça ayarlanabilir `Startup.Configure` . Aşağıdaki örnek Azure Active Directory düzenini ayarlar:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Belirteçleri bir uygulamaya geçirin Blazor Server</h2>

Bir uygulamadaki bileşenlerin dışında bulunan belirteçler, Razor Blazor Server Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.

Uygulamanın kimliğini Blazor Server düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın Razor . Belirteçleri sağlama ve kimlik doğrulamaya kaydetme cookie . Örneğin:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

İsteğe bağlı olarak, ek kapsamlar ile eklenir `options.Scope.Add("{SCOPE}");` , burada yer tutucu `{SCOPE}` Eklenecek ek kapsamdır.

İsteğe bağlı olarak, kaynak ile belirtilir; `options.Resource = "{RESOURCE}";` burada yer tutucu `{RESOURCE}` kaynak olur. Örneğin:

```csharp
options.Resource = "https://graph.microsoft.com";
```

Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

**scoped** Blazor [Bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:

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

`_Host.cshtml`Dosyasında, öğesini oluşturup örnek `InitialApplicationState` bir parametre olarak uygulamaya geçirin:

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

`App`Bileşeninde ( `App.razor` ), hizmeti çözümleyin ve parametresindeki verilerle başlatın:

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

NuGet paketi için uygulamaya bir paket başvurusu ekleyin [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .

Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API isteğinin belirtecini alın:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Kimlik doğrulama düzenini ayarlama</h2>

Birden fazla kimlik doğrulama ara yazılımı kullanan ve dolayısıyla birden fazla kimlik doğrulama şemasına sahip olan bir uygulama için, Blazor tarafından kullanılan şema, öğesinin uç nokta yapılandırmasında açıkça ayarlanabilir `Startup.Configure` . Aşağıdaki örnek Azure Active Directory düzenini ayarlar:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>OpenID Connect (OıDC) v 2.0 uç noktalarını kullan

5,0 ' dan önceki ASP.NET Core sürümlerinde, kimlik doğrulama kitaplığı ve Blazor Şablonlar OpenID Connect (OıDC) v 1.0 uç noktalarını kullanır. 5,0 ' den önceki ASP.NET Core sürümleriyle v 2.0 uç noktası kullanmak için, ' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> deki seçeneğini yapılandırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Alternatif olarak, ayar uygulama ayarları ( `appsettings.json` ) dosyasında yapılabilir:

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

  Daha fazla bilgi için bkz. Azure belgelerindeki [kaynakları değil kapsamları](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) .

### <a name="app-id-uri"></a>Uygulama Kimliği URI'si

* V 2.0 uç noktaları kullanılırken API 'Ler bir tanımlar *`App ID URI`* , bu, API için benzersiz bir tanımlayıcıyı temsil eder.
* Tüm kapsamlar, ön ek olarak uygulama KIMLIĞI URI 'sini içerir ve v 2.0 uç noktaları, uygulama KIMLIĞI URI 'SI ile birlikte erişim belirteçlerini hedef kitle olarak sunar.
* V 2.0 uç noktaları kullanılırken, sunucu API 'sinde yapılandırılan istemci KIMLIĞI API uygulama KIMLIĞI 'nden (Istemci KIMLIĞI) uygulama KIMLIĞI URI 'sine değişir.

`appsettings.json`:

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

::: moniker-end
