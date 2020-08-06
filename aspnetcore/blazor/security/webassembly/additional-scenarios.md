---
title: ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları
author: guardrex
description: Blazor WebAssemblyEk güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 81ab2bb139dfcbea712d4eb51acfc9d7f6767d46
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818840"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="5eb06-103">ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="5eb06-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="5eb06-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="5eb06-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="5eb06-105">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="5eb06-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="5eb06-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>, <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="5eb06-107">Belirteçler, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Framework tarafından kaydedilen hizmet kullanılarak alınır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="5eb06-108">Bir belirteç alınamadığından, bir oluşturulur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="5eb06-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="5eb06-110">Kolaylık olması için Framework, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> uygulamanın temel adresiyle önceden yapılandırılmış bir yetkili URL 'si sağlar.</span><span class="sxs-lookup"><span data-stu-id="5eb06-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="5eb06-111">**Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.**</span><span class="sxs-lookup"><span data-stu-id="5eb06-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="5eb06-112">Giden istek URI 'Leri uygulamanın temel URI 'SI içinde olmadığında, [özel bir sınıf kullanın `AuthorizationMessageHandler` (*önerilir*)](#custom-authorizationmessagehandler-class) veya öğesini [yapılandırın `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="5eb06-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="5eb06-113">Sunucu API 'SI erişimi için istemci uygulama yapılandırmasına ek olarak, istemci ve sunucu aynı temel adreste olmadığında sunucu API 'SI de çıkış noktaları arası isteklere (CORS) izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="5eb06-114">Sunucu tarafı CORS yapılandırması hakkında daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [çıkış noktaları arası kaynak paylaşımı (CORS)](#cross-origin-resource-sharing-cors) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="5eb06-115">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5eb06-115">In the following example:</span></span>

* <span data-ttu-id="5eb06-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A><xref:System.Net.Http.IHttpClientFactory>hizmet koleksiyonuna ve ilgili hizmetleri ekler ve adlandırılmış bir <xref:System.Net.Http.HttpClient> ( `ServerAPI` ) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="5eb06-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>, istek gönderilirken Kaynak URI 'sinin temel adresidir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="5eb06-118"><xref:System.Net.Http.IHttpClientFactory>, [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="5eb06-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>, <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="5eb06-120">Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="5eb06-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType><xref:System.Net.Http.HttpClient>adlandırılmış () öğesine karşılık gelen yapılandırmayı kullanarak giden istekler için bir örnek oluşturur ve yapılandırır <xref:System.Net.Http.HttpClient> `ServerAPI` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

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

<span data-ttu-id="5eb06-122">BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , Istek URI 'leri varsayılan olarak uygulamanın temel URI 'si içindedir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="5eb06-123">Bu nedenle, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Proje şablonundan oluşturulan bir uygulamada öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="5eb06-124">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="5eb06-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="5eb06-125">Özel `AuthorizationMessageHandler` sınıf</span><span class="sxs-lookup"><span data-stu-id="5eb06-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="5eb06-126">*Bu bölümdeki kılavuz, uygulamanın temel URI 'SI içinde olmayan URI 'lere giden istekleri yapan istemci uygulamalar için önerilir.*</span><span class="sxs-lookup"><span data-stu-id="5eb06-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="5eb06-127">Aşağıdaki örnekte, için bir özel sınıf <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> için olarak kullanılmak üzere genişletilir <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="5eb06-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>Bu işleyiciyi, bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="5eb06-129">Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="5eb06-130">`Program.Main`( `Program.cs` ) İçinde, `CustomAuthorizationMessageHandler` kapsamlı bir hizmet olarak kaydedilir ve <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpResponseMessage> bir adlandırılmış tarafından yapılan giden örnekler için olarak yapılandırılır <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="5eb06-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="5eb06-131">BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="5eb06-132">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="5eb06-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="5eb06-133">İstemcisinin <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) paket) oluşturulduğu yerde, <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="5eb06-134">İstek URI 'si, aşağıdaki örnekte olduğu gibi göreli bir URI ise, `ExampleAPIMethod` <xref:System.Net.Http.HttpClient.BaseAddress> istemci uygulaması isteği yaptığında ile birleştirilir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="5eb06-135">Yapılandırma`AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="5eb06-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="5eb06-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>, yöntemi kullanılarak yetkili URL 'Ler, kapsamlar ve dönüş URL 'SI ile yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="5eb06-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>, bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek için işleyiciyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="5eb06-138">Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="5eb06-139">İstek URI 'si göreli bir URI ise, ile birleştirilir <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="5eb06-140">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> () bir içinde yapılandırır `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="5eb06-141">BlazorBarındırılan proje şablonunu temel alan bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:</span><span class="sxs-lookup"><span data-stu-id="5eb06-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="5eb06-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="5eb06-143">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="5eb06-144">Girdiyseniz`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="5eb06-144">Typed `HttpClient`</span></span>

<span data-ttu-id="5eb06-145">Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="5eb06-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="5eb06-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="5eb06-147">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="5eb06-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5eb06-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="5eb06-149">BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="5eb06-150">`FetchData`bileşen ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5eb06-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="5eb06-151">İşleyiciyi yapılandırma `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="5eb06-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="5eb06-152">İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="5eb06-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5eb06-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="5eb06-154">BlazorBarındırılan proje şablonunu temel alan bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:</span><span class="sxs-lookup"><span data-stu-id="5eb06-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="5eb06-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="5eb06-156">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="5eb06-157">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="5eb06-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="5eb06-158">Blazor WebAssemblyUygulama genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="5eb06-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5eb06-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="5eb06-160">BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="5eb06-161">Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="5eb06-162">Bir bileşen, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) kimliği doğrulanmamış veya yetkisiz istekler yapmak için (paket) öğesinden öğesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5eb06-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="5eb06-163">Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="5eb06-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="5eb06-164">Varsayılan örnek, geliştiriciye yönelik olan güvenli istemci veya güvenli olmayan bir istemci kullanılıp kullanılmayacağını <xref:System.Net.Http.HttpClient> karardır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="5eb06-165">Bu kararı yapmanın bir yolu, uygulamanın ilgili olduğu kimliği doğrulanmamış ve kimliği doğrulanmamış uç noktaların sayısını göz önünde bulundurmanız.</span><span class="sxs-lookup"><span data-stu-id="5eb06-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="5eb06-166">Uygulama isteklerinin çoğunluğu API uç noktalarına güvense, <xref:System.Net.Http.HttpClient> Varsayılan olarak kimliği doğrulanmış örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="5eb06-167">Aksi takdirde, kimliği doğrulanmamış <xref:System.Net.Http.HttpClient> örneği varsayılan olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="5eb06-168">' Nin kullanılmasına alternatif bir yaklaşım, <xref:System.Net.Http.IHttpClientFactory> anonim uç noktalara kimliği doğrulanmamış erişim için [yazılmış bir istemci](#typed-httpclient) oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="5eb06-169">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="5eb06-169">Request additional access tokens</span></span>

<span data-ttu-id="5eb06-170">Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="5eb06-171">Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-171">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="5eb06-172">Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, Istemci uygulamasında ek kapsamlar yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-172">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="5eb06-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5eb06-173">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="5eb06-174">`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="5eb06-174">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="5eb06-175">Bir Razor bileşende:</span><span class="sxs-lookup"><span data-stu-id="5eb06-175">In a Razor component:</span></span>

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

<span data-ttu-id="5eb06-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="5eb06-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="5eb06-177">`true`' i `token` kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-177">`true` with the `token` for use.</span></span>
* <span data-ttu-id="5eb06-178">`false`belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="5eb06-178">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="5eb06-179">`HttpClient`ve `HttpRequestMessage` Fetch API isteği seçenekleriyle</span><span class="sxs-lookup"><span data-stu-id="5eb06-179">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="5eb06-180">Bir uygulamada WebAssembly üzerinde çalışırken Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) ([API belgeleri](xref:System.Net.Http.HttpClient)) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-180">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="5eb06-181">Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-181">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="5eb06-182">Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-182">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="5eb06-183">.NET WebAssembly 'ın uygulanması, <xref:System.Net.Http.HttpClient> [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-183">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="5eb06-184">Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-184">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="5eb06-185">HTTP getirme isteği seçenekleri <xref:System.Net.Http.HttpRequestMessage> , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-185">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="5eb06-186">Genişletme yöntemi</span><span class="sxs-lookup"><span data-stu-id="5eb06-186">Extension method</span></span> | <span data-ttu-id="5eb06-187">Fetch isteği özelliği</span><span class="sxs-lookup"><span data-stu-id="5eb06-187">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="5eb06-188">Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-188">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="5eb06-189">HTTP yanıtı, Blazor WebAssembly yanıt içeriği üzerinde eşitleme okuma desteğini etkinleştirmek için genellikle bir uygulamada arabelleğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-189">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="5eb06-190">Yanıt akışı desteğini etkinleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> istekteki genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-190">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="5eb06-191">Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5eb06-191">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="5eb06-192">API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="5eb06-192">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="5eb06-193">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="5eb06-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="5eb06-194">CORS isteklerindeki kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, bir `Authorization` üst BILGIYE CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-194">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="5eb06-195">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-195">The following policy includes configuration for:</span></span>

* <span data-ttu-id="5eb06-196">İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-196">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="5eb06-197">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="5eb06-197">Any method (verb).</span></span>
* <span data-ttu-id="5eb06-198">`Content-Type`ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-198">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="5eb06-199">Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-199">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="5eb06-200">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-200">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="5eb06-201">BlazorBarındırılan proje şablonunu temel alan barındırılan bir çözüm, Blazor istemci ve sunucu uygulamaları için aynı temel adresi kullanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-201">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="5eb06-202">İstemci uygulaması <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak BIR URI 'sine ayarlanır `builder.HostEnvironment.BaseAddress` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-202">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="5eb06-203">CORS yapılandırması, barındırılan proje şablonundan oluşturulan barındırılan bir uygulamanın varsayılan yapılandırmasında gerekli **değildir** Blazor .</span><span class="sxs-lookup"><span data-stu-id="5eb06-203">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="5eb06-204">Sunucu projesi tarafından barındırılmayan ve sunucu uygulamasının **temel adresini** paylaşmayan ek istemci uygulamaları, sunucu projesinde CORS yapılandırması gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="5eb06-204">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="5eb06-205">Daha fazla bilgi için bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-205">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="5eb06-206">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="5eb06-206">Handle token request errors</span></span>

<span data-ttu-id="5eb06-207">Tek sayfalı uygulama (SPA), OpenID Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve Identity sağlayıcıda (IP), kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanan bir oturum tanımlama bilgisi biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="5eb06-207">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="5eb06-208">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-208">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="5eb06-209">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-209">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="5eb06-210">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="5eb06-210">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="5eb06-211">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="5eb06-211">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="5eb06-212">Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-212">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="5eb06-213">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-213">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="5eb06-214">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-214">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="5eb06-215">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-215">They are part of the nature of SPAs.</span></span> <span data-ttu-id="5eb06-216">Kimlik doğrulama tanımlama bilgisi kaldırılırsa, tanımlama bilgilerini kullanan bir SPA da sunucu API 'sini çağıramaz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-216">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="5eb06-217">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-217">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="5eb06-218">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-218">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="5eb06-219">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-219">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="5eb06-220">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="5eb06-220">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="5eb06-221">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-221">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="5eb06-222">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-222">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="5eb06-223">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-223">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="5eb06-224">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="5eb06-224">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="5eb06-225">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-225">Store the current page state in session storage.</span></span> <span data-ttu-id="5eb06-226">[ `OnInitializedAsync` Yaşam döngüsü olayı](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) sırasında, devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="5eb06-226">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="5eb06-227">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-227">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="5eb06-228">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-228">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="5eb06-229">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-229">The following example shows how to:</span></span>

* <span data-ttu-id="5eb06-230">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="5eb06-230">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="5eb06-231">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-231">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="5eb06-232">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="5eb06-232">Save app state before an authentication operation</span></span>

<span data-ttu-id="5eb06-233">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-233">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="5eb06-234">Bu durum kapsayıcısını kullanırken ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-234">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="5eb06-235">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-235">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="5eb06-236">Aşağıdaki örnekte yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-236">The following example demonstrates the approach.</span></span>

<span data-ttu-id="5eb06-237">Uygulamanın durum değerlerini tutacak özelliklerle birlikte uygulamada bir durum kapsayıcı sınıfı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5eb06-237">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="5eb06-238">Aşağıdaki örnekte kapsayıcı, varsayılan proje şablonunun bileşeninin () sayaç değerini korumak için kullanılır `Counter` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-238">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="5eb06-239">Kapsayıcıyı serileştirmek ve seri durumdan çıkarmak için yöntemleri temel alır <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-239">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="5eb06-240">`Counter`Bileşen, değeri bileşenin dışında tutmak için durum kapsayıcısını kullanır `currentCount` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-240">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="5eb06-241">Konumundan bir `ApplicationAuthenticationState` oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-241">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="5eb06-242">`Id`Yerel olarak depolanmış durum için tanımlayıcı görevi gören bir özellik sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-242">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="5eb06-243">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="5eb06-243">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="5eb06-244">`Authentication`Bileşen ( `Pages/Authentication.razor` ), `StateContainer` serileştirme ve seri durumdan çıkarma yöntemleriyle yerel oturum depolamayı kullanarak uygulamanın durumunu kaydeder ve geri yükler `GetStateForLocalStorage` ve `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-244">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="5eb06-245">Bu örnek, kimlik doğrulaması için Azure Active Directory (AAD) kullanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-245">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="5eb06-246">İçinde `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5eb06-246">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="5eb06-247">, `ApplicationAuthenticationState` Microsoft autentication Library (msal) türü olarak yapılandırılır `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-247">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="5eb06-248">Durum kapsayıcısı, hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-248">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="5eb06-249">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5eb06-249">Customize app routes</span></span>

<span data-ttu-id="5eb06-250">Varsayılan olarak, [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-250">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="5eb06-251">Yol</span><span class="sxs-lookup"><span data-stu-id="5eb06-251">Route</span></span>                            | <span data-ttu-id="5eb06-252">Amaç</span><span class="sxs-lookup"><span data-stu-id="5eb06-252">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="5eb06-253">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-253">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="5eb06-254">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-254">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="5eb06-255">Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-255">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="5eb06-256">Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-256">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="5eb06-257">Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-257">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="5eb06-258">Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-258">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="5eb06-259">Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-259">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="5eb06-260">Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-260">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="5eb06-261">Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="5eb06-261">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="5eb06-262">Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-262">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5eb06-263">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-263">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="5eb06-264">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-264">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="5eb06-265">`Authentication`bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5eb06-265">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="5eb06-266">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="5eb06-266">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="5eb06-267">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> açık bir eylem parametresiyle işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="5eb06-267">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="5eb06-268">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-268">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="5eb06-269">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5eb06-269">Customize the authentication user interface</span></span>

<span data-ttu-id="5eb06-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="5eb06-271">Her durum, özel olarak geçirerek özelleştirilebilir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="5eb06-271">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="5eb06-272">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> aşağıdaki gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-272">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="5eb06-273">`Authentication`bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5eb06-273">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="5eb06-274">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-274">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="5eb06-275">Yol</span><span class="sxs-lookup"><span data-stu-id="5eb06-275">Route</span></span>                            | <span data-ttu-id="5eb06-276">Parça</span><span class="sxs-lookup"><span data-stu-id="5eb06-276">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="5eb06-277">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5eb06-277">Customize the user</span></span>

<span data-ttu-id="5eb06-278">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-278">Users bound to the app can be customized.</span></span> <span data-ttu-id="5eb06-279">Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-279">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="5eb06-280">Sınıfı genişleten bir sınıf oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="5eb06-280">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="5eb06-281">Şunu genişleten bir fabrika oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="5eb06-281">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="5eb06-282">`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun.</span><span class="sxs-lookup"><span data-stu-id="5eb06-282">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="5eb06-283">Aşağıdaki kayıtlardan herhangi biri geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-283">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="5eb06-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="5eb06-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="5eb06-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="5eb06-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="5eb06-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="5eb06-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="5eb06-287">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="5eb06-287">Support prerendering with authentication</span></span>

<span data-ttu-id="5eb06-288">Barındırılan uygulama konularından birindeki yönergeleri uyguladıktan sonra Blazor WebAssembly aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="5eb06-288">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="5eb06-289">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="5eb06-289">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="5eb06-290">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-290">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="5eb06-291">Istemci uygulamasının `Program` sınıfında ( `Program.cs` ), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin,) göre çarpanlara koyun `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-291">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="5eb06-292">Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="5eb06-292">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="5eb06-293">Sunucu uygulamasının `Startup.Configure` yönteminde, [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) ile değiştirin [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="5eb06-293">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="5eb06-294">Sunucu uygulamasında, yoksa bir klasör oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-294">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="5eb06-295">`_Host.cshtml`Sunucu uygulamasının klasörünün içinde bir sayfa oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-295">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="5eb06-296">İçeriği Istemci uygulamasının `wwwroot/index.html` dosyasından `Pages/_Host.cshtml` dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-296">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="5eb06-297">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="5eb06-297">Update the file's contents:</span></span>

* <span data-ttu-id="5eb06-298">`@page "_Host"`Dosyanın en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-298">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="5eb06-299">`<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5eb06-299">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="5eb06-300">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="5eb06-300">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="5eb06-301">Bir üçüncü taraf sağlayıcı ile barındırılan bir uygulamayı kimlik doğrulaması ve yetkilendirirken Blazor WebAssembly , kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-301">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="5eb06-302">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-302">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="5eb06-303">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="5eb06-303">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="5eb06-304">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="5eb06-304">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="5eb06-305">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="5eb06-305">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="5eb06-306">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="5eb06-306">In this scenario:</span></span>

* <span data-ttu-id="5eb06-307">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="5eb06-307">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="5eb06-308">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-308">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="5eb06-309">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-309">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="5eb06-310">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="5eb06-310">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="5eb06-311">IdentityBir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-311">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="5eb06-312">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-312">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="5eb06-313">Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-313">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="5eb06-314">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-314">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="5eb06-315">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="5eb06-315">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="5eb06-316">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-316">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="5eb06-317">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb06-317">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="5eb06-318">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="5eb06-318">We don't recommend this approach.</span></span> <span data-ttu-id="5eb06-319">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-319">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="5eb06-320">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5eb06-320">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="5eb06-321">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-321">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="5eb06-322">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-322">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="5eb06-323">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="5eb06-323">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="5eb06-324">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="5eb06-324">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="5eb06-325">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-325">Make an API call from the client to the server API.</span></span> <span data-ttu-id="5eb06-326">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="5eb06-326">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="5eb06-327">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="5eb06-327">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="5eb06-328">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-328">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="5eb06-329">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-329">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="5eb06-330">OpenID Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="5eb06-330">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="5eb06-331">Kimlik doğrulama kitaplığı ve Blazor Proje şablonları, OpenID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="5eb06-331">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="5eb06-332">Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-332">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="5eb06-333">Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="5eb06-333">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="5eb06-334">Alternatif olarak, ayar uygulama ayarları ( `appsettings.json` ) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="5eb06-334">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="5eb06-335">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-335">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="5eb06-336"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>Ya da uygulama ayarları dosyasındaki ( `appsettings.json` ) özelliğini anahtarıyla ayarlayın `Authority` .</span><span class="sxs-lookup"><span data-stu-id="5eb06-336">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="5eb06-337">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="5eb06-337">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="5eb06-338">Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="5eb06-338">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="5eb06-339">Bileşenlerinde gRPC 'yi yapılandırma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="5eb06-339">Configure and use gRPC in components</span></span>

<span data-ttu-id="5eb06-340">Blazor WebAssembly [ASP.NET Core GRPC çerçevesini](xref:grpc/index)kullanmak üzere bir uygulamayı yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="5eb06-340">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="5eb06-341">Sunucuda gRPC-Web ' i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-341">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="5eb06-342">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="5eb06-342">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="5eb06-343">Uygulamanın ileti işleyicisi için gRPC hizmetlerini kaydettirin.</span><span class="sxs-lookup"><span data-stu-id="5eb06-343">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="5eb06-344">Aşağıdaki örnek, uygulamanın yetkilendirme iletisi işleyicisini [ `GreeterClient` GRPC öğreticisindeki](xref:tutorials/grpc/grpc-start#create-a-grpc-service) () hizmetini kullanacak şekilde yapılandırır `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-344">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="5eb06-345">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-345">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="5eb06-346">`.proto`Dosyayı `Shared` barındırılan çözümün projesine yerleştirin Blazor .</span><span class="sxs-lookup"><span data-stu-id="5eb06-346">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="5eb06-347">İstemci uygulamasındaki bir bileşen GRPC istemcisini () kullanarak gRPC çağrıları yapabilir `Pages/Grpc.razor` :</span><span class="sxs-lookup"><span data-stu-id="5eb06-347">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="5eb06-348">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5eb06-348">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="5eb06-349">Özelliğini kullanmak için `Status.DebugException` , [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) Version 2.30.0 veya üstünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="5eb06-349">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="5eb06-350">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="5eb06-350">For more information, see <xref:grpc/browser>.</span></span>
