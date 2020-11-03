---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: ek güvenlik senaryoları'
author: guardrex
description: :::no-loc(Blazor Server):::Ek güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234445"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="b4708-103">ASP.NET Core :::no-loc(Blazor Server)::: ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="b4708-103">ASP.NET Core :::no-loc(Blazor Server)::: additional security scenarios</span></span>

<span data-ttu-id="b4708-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="b4708-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="b4708-105">Belirteçleri bir uygulamaya geçirin :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="b4708-105">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="b4708-106">Bir uygulamadaki bileşenlerin dışında bulunan belirteçler, :::no-loc(Razor)::: :::no-loc(Blazor Server)::: Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b4708-106">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="b4708-107">Uygulamanın kimliğini :::no-loc(Blazor Server)::: düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="b4708-107">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="b4708-108">Belirteçleri sağlama ve kimlik doğrulamaya kaydetme :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b4708-108">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="b4708-109">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b4708-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="b4708-110">İsteğe bağlı olarak, ek kapsamlar ile eklenir `options.Scope.Add("{SCOPE}");` , burada yer tutucu `{SCOPE}` Eklenecek ek kapsamdır.</span><span class="sxs-lookup"><span data-stu-id="b4708-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="b4708-111">**scoped** :::no-loc(Blazor)::: [Bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="b4708-111">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="b4708-112">' De `Startup.ConfigureServices` , için hizmetler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4708-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="b4708-113">Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="b4708-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="b4708-114">`_Host.cshtml`Dosyasında, öğesini oluşturup örnek `InitialApplicationState` bir parametre olarak uygulamaya geçirin:</span><span class="sxs-lookup"><span data-stu-id="b4708-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="b4708-115">`App`Bileşeninde ( `App.razor` ), hizmeti çözümleyin ve parametresindeki verilerle başlatın:</span><span class="sxs-lookup"><span data-stu-id="b4708-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="b4708-116">NuGet paketi için uygulamaya bir paket başvurusu ekleyin [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .</span><span class="sxs-lookup"><span data-stu-id="b4708-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="b4708-117">Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API isteğinin belirtecini alın:</span><span class="sxs-lookup"><span data-stu-id="b4708-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

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

<h2 id="set-the-authentication-scheme"><span data-ttu-id="b4708-118">Kimlik doğrulama düzenini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b4708-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="b4708-119">Birden fazla kimlik doğrulama ara yazılımı kullanan ve dolayısıyla birden fazla kimlik doğrulama şemasına sahip olan bir uygulama için, :::no-loc(Blazor)::: tarafından kullanılan şema, öğesinin uç nokta yapılandırmasında açıkça ayarlanabilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b4708-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="b4708-120">Aşağıdaki örnek Azure Active Directory düzenini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b4708-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="b4708-121">Belirteçleri bir uygulamaya geçirin :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="b4708-121">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="b4708-122">Bir uygulamadaki bileşenlerin dışında bulunan belirteçler, :::no-loc(Razor)::: :::no-loc(Blazor Server)::: Bu bölümde açıklanan yaklaşımla birlikte bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b4708-122">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="b4708-123">Uygulamanın kimliğini :::no-loc(Blazor Server)::: düzenli sayfalar veya MVC uygulamasıyla yaptığınız gibi doğrulayın :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="b4708-123">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="b4708-124">Belirteçleri sağlama ve kimlik doğrulamaya kaydetme :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b4708-124">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="b4708-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b4708-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="b4708-126">İsteğe bağlı olarak, ek kapsamlar ile eklenir `options.Scope.Add("{SCOPE}");` , burada yer tutucu `{SCOPE}` Eklenecek ek kapsamdır.</span><span class="sxs-lookup"><span data-stu-id="b4708-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="b4708-127">İsteğe bağlı olarak, kaynak ile belirtilir; `options.Resource = "{RESOURCE}";` burada yer tutucu `{RESOURCE}` kaynak olur.</span><span class="sxs-lookup"><span data-stu-id="b4708-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="b4708-128">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b4708-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="b4708-129">Erişim ve yenileme belirteçleriyle ilk uygulama durumunda geçirilecek bir sınıf tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="b4708-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="b4708-130">**scoped** :::no-loc(Blazor)::: [Bağımlılık ekleme (dı)](xref:blazor/fundamentals/dependency-injection)içindeki belirteçleri çözümlemek için uygulama içinde kullanılabilecek bir kapsamlı belirteç sağlayıcısı hizmeti tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="b4708-130">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="b4708-131">' De `Startup.ConfigureServices` , için hizmetler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4708-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="b4708-132">`_Host.cshtml`Dosyasında, öğesini oluşturup örnek `InitialApplicationState` bir parametre olarak uygulamaya geçirin:</span><span class="sxs-lookup"><span data-stu-id="b4708-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="b4708-133">`App`Bileşeninde ( `App.razor` ), hizmeti çözümleyin ve parametresindeki verilerle başlatın:</span><span class="sxs-lookup"><span data-stu-id="b4708-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="b4708-134">NuGet paketi için uygulamaya bir paket başvurusu ekleyin [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .</span><span class="sxs-lookup"><span data-stu-id="b4708-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="b4708-135">Güvenli bir API isteği yapan hizmette, belirteç sağlayıcısını ekler ve API isteğinin belirtecini alın:</span><span class="sxs-lookup"><span data-stu-id="b4708-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

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

<h2 id="set-the-authentication-scheme"><span data-ttu-id="b4708-136">Kimlik doğrulama düzenini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b4708-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="b4708-137">Birden fazla kimlik doğrulama ara yazılımı kullanan ve dolayısıyla birden fazla kimlik doğrulama şemasına sahip olan bir uygulama için, :::no-loc(Blazor)::: tarafından kullanılan şema, öğesinin uç nokta yapılandırmasında açıkça ayarlanabilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b4708-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="b4708-138">Aşağıdaki örnek Azure Active Directory düzenini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b4708-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="b4708-139">OpenID Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="b4708-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="b4708-140">5,0 ' dan önceki ASP.NET Core sürümlerinde, kimlik doğrulama kitaplığı ve :::no-loc(Blazor)::: Şablonlar OpenID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b4708-140">In versions of ASP.NET Core prior to 5.0, the authentication library and :::no-loc(Blazor)::: templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="b4708-141">5,0 ' den önceki ASP.NET Core sürümleriyle v 2.0 uç noktası kullanmak için, ' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> deki seçeneğini yapılandırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="b4708-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="b4708-142">Alternatif olarak, ayar uygulama ayarları ( `:::no-loc(appsettings.json):::` ) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="b4708-142">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="b4708-143">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b4708-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="b4708-144"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>Uygulama ayarları dosyasındaki ya da özelliğini <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> anahtarıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b4708-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="b4708-145">Kod değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b4708-145">Code changes</span></span>

* <span data-ttu-id="b4708-146">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="b4708-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="b4708-147">Daha fazla bilgi için bkz. [neden Microsoft Identity platform (v 2.0) güncelleştirmesi?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="b4708-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="b4708-148">Azure belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="b4708-148">in the Azure documentation.</span></span>
* <span data-ttu-id="b4708-149">Kaynaklar, v 2.0 uç noktaları için kapsam URI 'Lerinde belirtildiğinden, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> içindeki özellik ayarını kaldırın <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="b4708-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="b4708-150">Daha fazla bilgi için bkz. Azure belgelerindeki [kaynakları değil kapsamları](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) .</span><span class="sxs-lookup"><span data-stu-id="b4708-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="b4708-151">Uygulama Kimliği URI'si</span><span class="sxs-lookup"><span data-stu-id="b4708-151">App ID URI</span></span>

* <span data-ttu-id="b4708-152">V 2.0 uç noktaları kullanılırken API 'Ler bir tanımlar *`App ID URI`* , bu, API için benzersiz bir tanımlayıcıyı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b4708-152">When using v2.0 endpoints, APIs define an *`App ID URI`* , which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="b4708-153">Tüm kapsamlar, ön ek olarak uygulama KIMLIĞI URI 'sini içerir ve v 2.0 uç noktaları, uygulama KIMLIĞI URI 'SI ile birlikte erişim belirteçlerini hedef kitle olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="b4708-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="b4708-154">V 2.0 uç noktaları kullanılırken, sunucu API 'sinde yapılandırılan istemci KIMLIĞI API uygulama KIMLIĞI 'nden (Istemci KIMLIĞI) uygulama KIMLIĞI URI 'sine değişir.</span><span class="sxs-lookup"><span data-stu-id="b4708-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="b4708-155">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="b4708-155">`:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="b4708-156">OıDC sağlayıcısı uygulama kaydı açıklamasında kullanılacak uygulama KIMLIĞI URI 'sini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b4708-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
