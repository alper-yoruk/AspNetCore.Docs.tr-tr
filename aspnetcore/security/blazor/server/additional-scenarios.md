---
title: ASP.NET Core Blazor sunucusu ek güvenlik senaryoları
author: guardrex
description: Daha fazla güvenlik senaryosu Blazor için sunucu yapılandırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206391"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="bb415-103">ASP.NET Core Blazor sunucusu ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="bb415-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="bb415-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="bb415-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="bb415-105">Belirteçleri bir Blazor sunucu uygulamasına geçirme</span><span class="sxs-lookup"><span data-stu-id="bb415-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="bb415-106">Bir Blazor sunucu uygulamasındaki Razor bileşenleri dışında bulunan belirteçler, bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="bb415-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="bb415-107">Örnek kod için, tam `Startup.ConfigureServices` bir örnek de dahil olmak üzere [belirteçleri sunucu tarafı Blazor uygulamasına geçirme](https://github.com/javiercn/blazor-server-aad-sample)konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="bb415-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="bb415-108">Blazor Sunucu uygulamasının kimliğini düzenli bir Razor Pages veya MVC uygulamasıyla yaptığınız gibi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="bb415-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="bb415-109">Belirteçleri sağlama ve kimlik doğrulama tanımlama bilgisine kaydetme.</span><span class="sxs-lookup"><span data-stu-id="bb415-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="bb415-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="bb415-110">For example:</span></span>

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

<span data-ttu-id="bb415-111">Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="bb415-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="bb415-112">[Bağımlılık ekleme (dı)](xref:blazor/dependency-injection)içindeki belirteçleri çözümlemek için Blazor uygulama içinde kullanılabilecek bir **kapsamlı** belirteç sağlayıcısı hizmeti tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="bb415-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="bb415-113">' `Startup.ConfigureServices`De, için hizmetler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bb415-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="bb415-114">*_Host. cshtml* dosyasında, oluşturun ve örneğini `InitialApplicationState` uygulamaya bir parametre olarak geçirin:</span><span class="sxs-lookup"><span data-stu-id="bb415-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="bb415-115">`App` Bileşeninde (*app. Razor*), hizmeti çözümleyin ve bu parametreyi parametresindeki verilerle başlatın:</span><span class="sxs-lookup"><span data-stu-id="bb415-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="bb415-116">Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API 'YI çağırmak için belirteci alın:</span><span class="sxs-lookup"><span data-stu-id="bb415-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
