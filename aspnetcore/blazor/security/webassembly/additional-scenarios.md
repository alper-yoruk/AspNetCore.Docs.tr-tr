---
title: ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları
author: guardrex
description: Blazor WebAssemblyEk güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 79f7b2177d6d07101c73cde841c062b0e1468593
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445157"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="f6641-103">ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="f6641-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="f6641-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="f6641-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="f6641-105">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="f6641-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="f6641-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Hizmeti, <xref:System.Net.Http.HttpClient> giden isteklere erişim belirteçleri eklemek için ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="f6641-107">Belirteçler, mevcut hizmet kullanılarak alınır <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> .</span><span class="sxs-lookup"><span data-stu-id="f6641-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="f6641-108">Bir belirteç alınamadığından, bir oluşturulur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> .</span><span class="sxs-lookup"><span data-stu-id="f6641-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="f6641-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f6641-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="f6641-110">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> Yöntemi kullanılarak yetkili URL 'ler, kapsamlar ve dönüş URL 'si ile yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="f6641-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="f6641-111">Giden istekler için bir ileti işleyicisini yapılandırmak üzere aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f6641-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="f6641-112">[Özel `AuthorizationMessageHandler` sınıf](#custom-authorizationmessagehandler-class) (*önerilir*)</span><span class="sxs-lookup"><span data-stu-id="f6641-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="f6641-113">Yapılandırma`AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="f6641-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="f6641-114">Özel AuthorizationMessageHandler sınıfı</span><span class="sxs-lookup"><span data-stu-id="f6641-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="f6641-115">Aşağıdaki örnekte, öğesini yapılandırmak için kullanılabilecek özel bir sınıf genişletilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="f6641-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="f6641-116">`Program.Main`( `Program.cs` ) İçinde, bir <xref:System.Net.Http.HttpClient> özel yetkilendirme iletisi işleyicisiyle yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="f6641-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="f6641-117">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) öğesine atanabilir <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f6641-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6641-118">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="f6641-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="f6641-119">İstemcisinin <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) paket) oluşturulduğu yerde, <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlanır:</span><span class="sxs-lookup"><span data-stu-id="f6641-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="f6641-120">AuthorizationMessageHandler 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f6641-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="f6641-121">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> () bir içinde yapılandırır `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f6641-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="f6641-122">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Şuna atanabilir:</span><span class="sxs-lookup"><span data-stu-id="f6641-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="f6641-123"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="f6641-124">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="f6641-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="f6641-125">Kolaylık olması için, bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> yetkılı URL olarak uygulamanın temel adresiyle önceden yapılandırılmış bir içerir.</span><span class="sxs-lookup"><span data-stu-id="f6641-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="f6641-126">Kimlik doğrulama etkin Blazor WebAssembly Şablonlar <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) , bir Ile ayarlamak için sunucu API projesinde (paket) kullanır <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="f6641-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="f6641-127">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) öğesine atanabilir <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f6641-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6641-128">Önceki örnekte istemci oluşturulduğu yerde <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> , <xref:System.Net.Http.HttpClient> sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f6641-129">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="f6641-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="f6641-130">Yazılan HttpClient</span><span class="sxs-lookup"><span data-stu-id="f6641-130">Typed HttpClient</span></span>

<span data-ttu-id="f6641-131">Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="f6641-132">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6641-132">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="f6641-133">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="f6641-134">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f6641-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="f6641-135">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) öğesine atanabilir <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f6641-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6641-136">`FetchData`bileşen ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f6641-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="f6641-137">HttpClient işleyicisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f6641-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="f6641-138">İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="f6641-139">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f6641-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="f6641-140">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Şuna atanabilir:</span><span class="sxs-lookup"><span data-stu-id="f6641-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="f6641-141"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="f6641-142">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="f6641-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="f6641-143">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="f6641-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="f6641-144">Blazor WebAssemblyUygulama genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="f6641-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="f6641-145">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f6641-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="f6641-146">BlazorBarındırılan şablona dayalı bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) öğesine atanabilir <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f6641-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="f6641-147">Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="f6641-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="f6641-148">Bir bileşen, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) kimliği doğrulanmamış veya yetkisiz istekler yapmak için (paket) öğesinden öğesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f6641-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="f6641-149">Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="f6641-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="f6641-150">Varsayılan örnek, geliştiriciye yönelik olan güvenli istemci veya güvenli olmayan bir istemci kullanılıp kullanılmayacağını <xref:System.Net.Http.HttpClient> karardır.</span><span class="sxs-lookup"><span data-stu-id="f6641-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="f6641-151">Bu kararı yapmanın bir yolu, uygulamanın ilgili olduğu kimliği doğrulanmamış ve kimliği doğrulanmamış uç noktaların sayısını göz önünde bulundurmanız.</span><span class="sxs-lookup"><span data-stu-id="f6641-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="f6641-152">Uygulama isteklerinin çoğunluğu API uç noktalarına güvense, <xref:System.Net.Http.HttpClient> Varsayılan olarak kimliği doğrulanmış örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="f6641-153">Aksi takdirde, kimliği doğrulanmamış <xref:System.Net.Http.HttpClient> örneği varsayılan olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f6641-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="f6641-154">' Nin kullanılmasına alternatif bir yaklaşım, <xref:System.Net.Http.IHttpClientFactory> anonim uç noktalara kimliği doğrulanmamış erişim için [yazılmış bir istemci](#typed-httpclient) oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="f6641-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="f6641-155">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="f6641-155">Request additional access tokens</span></span>

<span data-ttu-id="f6641-156">Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="f6641-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="f6641-157">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6641-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="f6641-158">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, Istemci uygulamasında ek kapsamlar yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f6641-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="f6641-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f6641-159">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="f6641-160">`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6641-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="f6641-161">Bir Razor bileşende:</span><span class="sxs-lookup"><span data-stu-id="f6641-161">In a Razor component:</span></span>

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

<span data-ttu-id="f6641-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="f6641-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="f6641-163">`true`' i `token` kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="f6641-164">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="f6641-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="f6641-165">API istek seçeneklerini getiren HttpClient ve HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="f6641-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="f6641-166">Bir uygulamada WebAssembly üzerinde çalışırken Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) ([API belgeleri](xref:System.Net.Http.HttpClient)) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="f6641-167">Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f6641-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="f6641-168">Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="f6641-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="f6641-169">.NET WebAssembly 'ın uygulanması, <xref:System.Net.Http.HttpClient> [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="f6641-170">Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="f6641-171">HTTP getirme isteği seçenekleri <xref:System.Net.Http.HttpRequestMessage> , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="f6641-172">Genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="f6641-172">Extension method</span></span> | <span data-ttu-id="f6641-173">Fetch isteği özelliği</span><span class="sxs-lookup"><span data-stu-id="f6641-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="f6641-174">Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="f6641-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="f6641-175">HTTP yanıtı, Blazor WebAssembly yanıt içeriği üzerinde eşitleme okuma desteğini etkinleştirmek için genellikle bir uygulamada arabelleğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="f6641-176">Yanıt akışı desteğini etkinleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> istekteki genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="f6641-177">Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f6641-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="f6641-178">API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="f6641-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="f6641-179">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="f6641-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="f6641-180">CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, bir `Authorization` üst BILGIYE CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f6641-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="f6641-181">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="f6641-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="f6641-182">İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="f6641-183">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="f6641-183">Any method (verb).</span></span>
* <span data-ttu-id="f6641-184">`Content-Type`ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="f6641-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="f6641-185">Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="f6641-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="f6641-186">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="f6641-187">Daha fazla bilgi için bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="f6641-188">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="f6641-188">Handle token request errors</span></span>

<span data-ttu-id="f6641-189">Tek sayfalı uygulama (SPA), açma KIMLIĞI Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve Identity sağlayıcıda (IP), kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanmış bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="f6641-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="f6641-190">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6641-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="f6641-191">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="f6641-192">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="f6641-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="f6641-193">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="f6641-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="f6641-194">Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="f6641-195">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="f6641-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="f6641-196">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="f6641-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="f6641-197">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6641-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="f6641-198">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="f6641-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="f6641-199">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="f6641-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="f6641-200">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="f6641-201">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="f6641-202">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="f6641-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="f6641-203">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="f6641-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="f6641-204">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6641-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="f6641-205">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6641-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="f6641-206">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="f6641-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="f6641-207">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="f6641-207">Store the current page state in session storage.</span></span> <span data-ttu-id="f6641-208">[ `OnInitializedAsync` Yaşam döngüsü olayı](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) sırasında, devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f6641-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="f6641-209">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="f6641-210">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6641-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="f6641-211">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f6641-211">The following example shows how to:</span></span>

* <span data-ttu-id="f6641-212">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="f6641-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="f6641-213">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="f6641-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="f6641-214">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="f6641-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="f6641-215">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="f6641-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="f6641-216">Bu durum kapsayıcısını kullanırken ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="f6641-217">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f6641-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="f6641-218">Aşağıdaki örnekte yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f6641-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="f6641-219">Uygulamanın durum değerlerini tutacak özelliklerle birlikte uygulamada bir durum kapsayıcı sınıfı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f6641-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="f6641-220">Aşağıdaki örnekte kapsayıcı, varsayılan şablon bileşeninin () sayaç değerini korumak için kullanılır `Counter` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="f6641-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="f6641-221">Kapsayıcıyı serileştirmek ve seri durumdan çıkarmak için yöntemleri temel alır <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="f6641-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="f6641-222">`Counter`Bileşen, değeri bileşenin dışında tutmak için durum kapsayıcısını kullanır `currentCount` :</span><span class="sxs-lookup"><span data-stu-id="f6641-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="f6641-223">Konumundan bir `ApplicationAuthenticationState` oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="f6641-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="f6641-224">`Id`Yerel olarak depolanmış durum için tanımlayıcı görevi gören bir özellik sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f6641-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="f6641-225">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6641-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="f6641-226">`Authentication`Bileşen ( `Pages/Authentication.razor` ), `StateContainer` serileştirme ve seri durumdan çıkarma yöntemleriyle yerel oturum depolamayı kullanarak uygulamanın durumunu kaydeder ve geri yükler `GetStateForLocalStorage` ve `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="f6641-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="f6641-227">Bu örnek, kimlik doğrulaması için Azure Active Directory (AAD) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="f6641-228">İçinde `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="f6641-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="f6641-229">, `ApplicationAuthenticationState` Microsoft autentication Library (msal) türü olarak yapılandırılır `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="f6641-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="f6641-230">Durum kapsayıcısı, hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="f6641-231">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f6641-231">Customize app routes</span></span>

<span data-ttu-id="f6641-232">Varsayılan olarak, [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="f6641-233">Yol</span><span class="sxs-lookup"><span data-stu-id="f6641-233">Route</span></span>                            | <span data-ttu-id="f6641-234">Amaç</span><span class="sxs-lookup"><span data-stu-id="f6641-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="f6641-235">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="f6641-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="f6641-236">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="f6641-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="f6641-237">Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f6641-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="f6641-238">Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="f6641-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="f6641-239">Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="f6641-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="f6641-240">Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f6641-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="f6641-241">Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f6641-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="f6641-242">Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="f6641-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="f6641-243">Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="f6641-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="f6641-244">Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f6641-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f6641-245">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f6641-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="f6641-246">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .</span><span class="sxs-lookup"><span data-stu-id="f6641-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="f6641-247">`Authentication`bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f6641-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f6641-248">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f6641-248">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="f6641-249">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> açık bir eylem parametresiyle işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="f6641-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="f6641-250">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="f6641-251">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f6641-251">Customize the authentication user interface</span></span>

<span data-ttu-id="f6641-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f6641-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="f6641-253">Her durum, özel olarak geçirerek özelleştirilebilir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="f6641-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="f6641-254">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> aşağıdaki gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="f6641-255">`Authentication`bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f6641-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="f6641-256">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f6641-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="f6641-257">Yol</span><span class="sxs-lookup"><span data-stu-id="f6641-257">Route</span></span>                            | <span data-ttu-id="f6641-258">Parça</span><span class="sxs-lookup"><span data-stu-id="f6641-258">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="f6641-259">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f6641-259">Customize the user</span></span>

<span data-ttu-id="f6641-260">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="f6641-261">Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="f6641-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="f6641-262">Sınıfı genişleten bir sınıf oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="f6641-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="f6641-263">Şunu genişleten bir fabrika oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="f6641-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="f6641-264">`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun.</span><span class="sxs-lookup"><span data-stu-id="f6641-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="f6641-265">Aşağıdaki kayıtlardan herhangi biri geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="f6641-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="f6641-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="f6641-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="f6641-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="f6641-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="f6641-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="f6641-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="f6641-269">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="f6641-269">Support prerendering with authentication</span></span>

<span data-ttu-id="f6641-270">Barındırılan uygulama konularından birindeki yönergeleri uyguladıktan sonra Blazor WebAssembly aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f6641-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="f6641-271">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="f6641-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="f6641-272">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="f6641-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="f6641-273">Istemci uygulamasının `Program` sınıfında ( `Program.cs` ), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin,) göre çarpanlara koyun `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="f6641-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

<span data-ttu-id="f6641-274">Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f6641-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="f6641-275">Sunucu uygulamasının `Startup.Configure` yönteminde, [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) ile değiştirin [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="f6641-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="f6641-276">Sunucu uygulamasında, yoksa bir klasör oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="f6641-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="f6641-277">`_Host.cshtml`Sunucu uygulamasının klasörünün içinde bir sayfa oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="f6641-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="f6641-278">İçeriği Istemci uygulamasının `wwwroot/index.html` dosyasından `Pages/_Host.cshtml` dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f6641-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="f6641-279">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6641-279">Update the file's contents:</span></span>

* <span data-ttu-id="f6641-280">`@page "_Host"`Dosyanın en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6641-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="f6641-281">`<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6641-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="f6641-282">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="f6641-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="f6641-283">Bir üçüncü taraf sağlayıcı ile barındırılan bir uygulamayı kimlik doğrulaması ve yetkilendirirken Blazor WebAssembly , kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="f6641-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="f6641-284">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f6641-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="f6641-285">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f6641-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="f6641-286">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="f6641-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="f6641-287">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="f6641-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="f6641-288">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="f6641-288">In this scenario:</span></span>

* <span data-ttu-id="f6641-289">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="f6641-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="f6641-290">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="f6641-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="f6641-291">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="f6641-292">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="f6641-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="f6641-293">IdentityBir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f6641-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="f6641-294">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="f6641-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="f6641-295">Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin.</span><span class="sxs-lookup"><span data-stu-id="f6641-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="f6641-296">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="f6641-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="f6641-297">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="f6641-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="f6641-298">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="f6641-299">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın Identity .</span><span class="sxs-lookup"><span data-stu-id="f6641-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="f6641-300">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="f6641-300">We don't recommend this approach.</span></span> <span data-ttu-id="f6641-301">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f6641-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="f6641-302">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f6641-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="f6641-303">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="f6641-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="f6641-304">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="f6641-305">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6641-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="f6641-306">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="f6641-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="f6641-307">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="f6641-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="f6641-308">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="f6641-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="f6641-309">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="f6641-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="f6641-310">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="f6641-311">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f6641-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="f6641-312">Açık KIMLIK Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="f6641-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="f6641-313">Kimlik doğrulama kitaplığı ve Blazor şablonları Open ID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6641-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="f6641-314">Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f6641-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="f6641-315">Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="f6641-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="f6641-316">Alternatif olarak, ayar uygulama ayarları ( `appsettings.json` ) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="f6641-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="f6641-317">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6641-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="f6641-318"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>Ya da uygulama ayarları dosyasındaki ( `appsettings.json` ) özelliğini anahtarıyla ayarlayın `Authority` .</span><span class="sxs-lookup"><span data-stu-id="f6641-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="f6641-319">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="f6641-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="f6641-320">Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="f6641-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="f6641-321">Bileşenlerinde gRPC 'yi yapılandırma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="f6641-321">Configure and use gRPC in components</span></span>

<span data-ttu-id="f6641-322">Blazor WebAssembly [ASP.NET Core GRPC çerçevesini](xref:grpc/index)kullanmak üzere bir uygulamayı yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="f6641-322">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="f6641-323">Sunucuda gRPC-Web ' i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6641-323">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="f6641-324">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="f6641-324">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="f6641-325">Uygulamanın ileti işleyicisi için gRPC hizmetlerini kaydettirin.</span><span class="sxs-lookup"><span data-stu-id="f6641-325">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="f6641-326">Aşağıdaki örnek, uygulamanın yetkilendirme iletisi işleyicisini [ `GreeterClient` GRPC öğreticisindeki](xref:tutorials/grpc/grpc-start#create-a-grpc-service) () hizmetini kullanacak şekilde yapılandırır `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f6641-326">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="f6641-327">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-327">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="f6641-328">`.proto`Dosyayı `Shared` barındırılan çözümün projesine yerleştirin Blazor .</span><span class="sxs-lookup"><span data-stu-id="f6641-328">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="f6641-329">İstemci uygulamasındaki bir bileşen GRPC istemcisini () kullanarak gRPC çağrıları yapabilir `Pages/Grpc.razor` :</span><span class="sxs-lookup"><span data-stu-id="f6641-329">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="f6641-330">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f6641-330">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="f6641-331">Özelliğini kullanmak için `Status.DebugException` , [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) Version 2.30.0 veya üstünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6641-331">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="f6641-332">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="f6641-332">For more information, see <xref:grpc/browser>.</span></span>
