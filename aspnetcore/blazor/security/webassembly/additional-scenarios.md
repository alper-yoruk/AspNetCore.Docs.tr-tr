---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: ek güvenlik senaryoları'
author: guardrex
description: :::no-loc(Blazor WebAssembly):::Ek güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 055e248abfadd9092c173e4630e56ea69517da3b
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690588"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ac7b1-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: ek güvenlik senaryoları</span><span class="sxs-lookup"><span data-stu-id="ac7b1-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: additional security scenarios</span></span>

<span data-ttu-id="ac7b1-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="ac7b1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ac7b1-105">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="ac7b1-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ac7b1-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="ac7b1-107">Belirteçler, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Framework tarafından kaydedilen hizmet kullanılarak alınır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="ac7b1-108">Bir belirteç alınamadığından, bir oluşturulur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="ac7b1-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="ac7b1-110">Kolaylık olması için Framework, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> uygulamanın temel adresiyle önceden yapılandırılmış bir yetkili URL 'si sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="ac7b1-111">**Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.**</span><span class="sxs-lookup"><span data-stu-id="ac7b1-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="ac7b1-112">Giden istek URI 'Leri uygulamanın temel URI 'SI içinde olmadığında, [özel bir sınıf kullanın `AuthorizationMessageHandler` ( *önerilir* )](#custom-authorizationmessagehandler-class) veya öğesini [yapılandırın `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class ( *recommended* )](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="ac7b1-113">Sunucu API 'SI erişimi için istemci uygulama yapılandırmasına ek olarak, istemci ve sunucu aynı temel adreste olmadığında sunucu API 'SI de çıkış noktaları arası isteklere (CORS) izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="ac7b1-114">Sunucu tarafı CORS yapılandırması hakkında daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [çıkış noktaları arası kaynak paylaşımı (CORS)](#cross-origin-resource-sharing-cors) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="ac7b1-115">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-115">In the following example:</span></span>

* <span data-ttu-id="ac7b1-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A><xref:System.Net.Http.IHttpClientFactory>hizmet koleksiyonuna ve ilgili hizmetleri ekler ve adlandırılmış bir <xref:System.Net.Http.HttpClient> ( `ServerAPI` ) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="ac7b1-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> , istek gönderilirken Kaynak URI 'sinin temel adresidir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="ac7b1-118"><xref:System.Net.Http.IHttpClientFactory> , [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="ac7b1-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> , <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="ac7b1-120">Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="ac7b1-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType><xref:System.Net.Http.HttpClient>adlandırılmış () öğesine karşılık gelen yapılandırmayı kullanarak giden istekler için bir örnek oluşturur ve yapılandırır <xref:System.Net.Http.HttpClient> `ServerAPI` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

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

<span data-ttu-id="ac7b1-122">:::no-loc(Blazor):::Barındırılan proje şablonuna dayalı bir uygulama için :::no-loc(Blazor WebAssembly)::: , Istek URI 'leri varsayılan olarak uygulamanın temel URI 'si içindedir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-122">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="ac7b1-123">Bu nedenle, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Proje şablonundan oluşturulan bir uygulamada öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="ac7b1-124">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="ac7b1-125">Özel `AuthorizationMessageHandler` sınıf</span><span class="sxs-lookup"><span data-stu-id="ac7b1-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="ac7b1-126">*Bu bölümdeki kılavuz, uygulamanın temel URI 'SI içinde olmayan URI 'lere giden istekleri yapan istemci uygulamalar için önerilir.*</span><span class="sxs-lookup"><span data-stu-id="ac7b1-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="ac7b1-127">Aşağıdaki örnekte, için bir özel sınıf <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> için olarak kullanılmak üzere genişletilir <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="ac7b1-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Bu işleyiciyi, bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="ac7b1-129">Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="ac7b1-130">`Program.Main`( `Program.cs` ) İçinde, `CustomAuthorizationMessageHandler` kapsamlı bir hizmet olarak kaydedilir ve <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpResponseMessage> bir adlandırılmış tarafından yapılan giden örnekler için olarak yapılandırılır <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="ac7b1-131">:::no-loc(Blazor):::Barındırılan proje şablonuna dayalı bir uygulama için :::no-loc(Blazor WebAssembly)::: , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-131">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="ac7b1-132">Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="ac7b1-133">İstemcisinin <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) paket) oluşturulduğu yerde, <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="ac7b1-134">İstek URI 'si, aşağıdaki örnekte olduğu gibi göreli bir URI ise, `ExampleAPIMethod` <xref:System.Net.Http.HttpClient.BaseAddress> istemci uygulaması isteği yaptığında ile birleştirilir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="ac7b1-135">Yapılandırma `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="ac7b1-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="ac7b1-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , yöntemi kullanılarak yetkili URL 'Ler, kapsamlar ve dönüş URL 'SI ile yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="ac7b1-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> , bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek için işleyiciyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="ac7b1-138">Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="ac7b1-139">İstek URI 'si göreli bir URI ise, ile birleştirilir <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="ac7b1-140">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> () bir içinde yapılandırır `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="ac7b1-141">:::no-loc(Blazor):::Barındırılan proje şablonunu temel alan bir uygulama için :::no-loc(Blazor WebAssembly)::: <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-141">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="ac7b1-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="ac7b1-143">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="ac7b1-144">Girdiyseniz `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="ac7b1-144">Typed `HttpClient`</span></span>

<span data-ttu-id="ac7b1-145">Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ac7b1-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="ac7b1-147">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `using static :::no-loc(Blazor):::Sample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static :::no-loc(Blazor):::Sample.Data;`).</span></span>

<span data-ttu-id="ac7b1-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ac7b1-149">:::no-loc(Blazor):::Barındırılan proje şablonuna dayalı bir uygulama için :::no-loc(Blazor WebAssembly)::: , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-149">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="ac7b1-150">`FetchData` bileşen ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ac7b1-151">İşleyiciyi yapılandırma `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="ac7b1-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="ac7b1-152">İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ac7b1-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="ac7b1-154">:::no-loc(Blazor):::Barındırılan proje şablonunu temel alan bir uygulama için :::no-loc(Blazor WebAssembly)::: <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-154">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="ac7b1-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="ac7b1-156">Dizinin URL 'SI `authorizedUrls` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ac7b1-157">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="ac7b1-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ac7b1-158">:::no-loc(Blazor WebAssembly):::Uygulama genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-158">If the :::no-loc(Blazor WebAssembly)::: app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ac7b1-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="ac7b1-160">:::no-loc(Blazor):::Barındırılan proje şablonuna dayalı bir uygulama için :::no-loc(Blazor WebAssembly)::: , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-160">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="ac7b1-161">Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ac7b1-162">Bir bileşen, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) kimliği doğrulanmamış veya yetkisiz istekler yapmak için (paket) öğesinden öğesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="ac7b1-163">Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="ac7b1-164">Varsayılan örnek, geliştiriciye yönelik olan güvenli istemci veya güvenli olmayan bir istemci kullanılıp kullanılmayacağını <xref:System.Net.Http.HttpClient> karardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="ac7b1-165">Bu kararı yapmanın bir yolu, uygulamanın ilgili olduğu kimliği doğrulanmamış ve kimliği doğrulanmamış uç noktaların sayısını göz önünde bulundurmanız.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="ac7b1-166">Uygulama isteklerinin çoğunluğu API uç noktalarına güvense, <xref:System.Net.Http.HttpClient> Varsayılan olarak kimliği doğrulanmış örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="ac7b1-167">Aksi takdirde, kimliği doğrulanmamış <xref:System.Net.Http.HttpClient> örneği varsayılan olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="ac7b1-168">' Nin kullanılmasına alternatif bir yaklaşım, <xref:System.Net.Http.IHttpClientFactory> anonim uç noktalara kimliği doğrulanmamış erişim için [yazılmış bir istemci](#typed-httpclient) oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ac7b1-169">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="ac7b1-169">Request additional access tokens</span></span>

<span data-ttu-id="ac7b1-170">Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="ac7b1-171">Aşağıdaki örnekte, bir uygulama için varsayılan olarak ek bir kapsam gerekir <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="ac7b1-172">Microsoft kimlik doğrulama kitaplığı (MSAL) örneği kapsamı şu şekilde yapılandırır `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="ac7b1-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="ac7b1-174">`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` Önceki örnekteki ve yer tutucular özel kapsamlardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="ac7b1-175">`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ac7b1-176">Bir :::no-loc(Razor)::: bileşende:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-176">In a :::no-loc(Razor)::: component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="ac7b1-177">`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` Önceki örnekteki ve yer tutucular özel kapsamlardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="ac7b1-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> döndürdüğü</span><span class="sxs-lookup"><span data-stu-id="ac7b1-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="ac7b1-179">`true` ' i `token` kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ac7b1-180">`false` belirteç alınmadıysa.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="ac7b1-181">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="ac7b1-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="ac7b1-182">CORS isteklerindeki kimlik bilgileri (yetkilendirme :::no-loc(cookie)::: s/üst bilgileri) gönderilirken, bir `Authorization` üst bilgiye CORS ilkesi tarafından izin verilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-182">When sending credentials (authorization :::no-loc(cookie):::s/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ac7b1-183">Aşağıdaki ilke için yapılandırma içerir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ac7b1-184">İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ac7b1-185">Any yöntemi (fiil).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-185">Any method (verb).</span></span>
* <span data-ttu-id="ac7b1-186">`Content-Type` ve `Authorization` üst bilgiler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ac7b1-187">Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ac7b1-188">İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ac7b1-189">:::no-loc(Blazor):::Barındırılan proje şablonunu temel alan barındırılan bir çözüm, :::no-loc(Blazor)::: istemci ve sunucu uygulamaları için aynı temel adresi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-189">A hosted :::no-loc(Blazor)::: solution based on the :::no-loc(Blazor)::: Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="ac7b1-190">İstemci uygulaması <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak BIR URI 'sine ayarlanır `builder.HostEnvironment.BaseAddress` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="ac7b1-191">CORS yapılandırması, barındırılan proje şablonundan oluşturulan barındırılan bir uygulamanın varsayılan yapılandırmasında gerekli **değildir** :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-191">CORS configuration is **not** required in the default configuration of a hosted app created from the :::no-loc(Blazor)::: Hosted project template.</span></span> <span data-ttu-id="ac7b1-192">Sunucu projesi tarafından barındırılmayan ve sunucu uygulamasının **temel adresini** paylaşmayan ek istemci uygulamaları, sunucu projesinde CORS yapılandırması gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="ac7b1-193">Daha fazla bilgi için bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ac7b1-194">Tanıtıcı belirteci isteği hataları</span><span class="sxs-lookup"><span data-stu-id="ac7b1-194">Handle token request errors</span></span>

<span data-ttu-id="ac7b1-195">Tek sayfalı uygulama (SPA), OpenID Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve :::no-loc(Identity)::: sağlayıcıda (IP), :::no-loc(cookie)::: kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanmış bir oturum biçiminde tutulur.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the :::no-loc(Identity)::: Provider (IP) in the form of a session :::no-loc(cookie)::: that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ac7b1-196">IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ac7b1-197">Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ac7b1-198">Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ac7b1-199">İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ac7b1-200">Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ac7b1-201">Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ac7b1-202">Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ac7b1-203">Bunlar, maça doğaları 'nın bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ac7b1-204">:::no-loc(cookie):::Kimlik doğrulaması kaldırılırsa, s kullanan BIR Spa da sunucu API 'sini çağıramaz :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-204">An SPA using :::no-loc(cookie):::s also fails to call a server API if the authentication :::no-loc(cookie)::: is removed.</span></span>

<span data-ttu-id="ac7b1-205">Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ac7b1-206">API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ac7b1-207">İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ac7b1-208">Uygulama bir belirteç istediğinde, olası iki sonuç vardır:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ac7b1-209">İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ac7b1-210">İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ac7b1-211">Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ac7b1-212">Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ac7b1-213">Geçerli sayfa durumunu oturum depolama alanında depolayın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-213">Store the current page state in session storage.</span></span> <span data-ttu-id="ac7b1-214">[ `OnInitializedAsync` Yaşam döngüsü olayı](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) sırasında, devam etmeden önce durumun geri yüklenebildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ac7b1-215">Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ac7b1-216">Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ac7b1-217">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-217">The following example shows how to:</span></span>

* <span data-ttu-id="ac7b1-218">Oturum açma sayfasına yönlendirmeden önce durumu koru.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ac7b1-219">Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ac7b1-220">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="ac7b1-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="ac7b1-221">Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ac7b1-222">Bu durum kapsayıcısını kullanırken ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ac7b1-223">Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="ac7b1-224">Aşağıdaki örnekte yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="ac7b1-225">Uygulamanın durum değerlerini tutacak özelliklerle birlikte uygulamada bir durum kapsayıcı sınıfı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="ac7b1-226">Aşağıdaki örnekte kapsayıcı, varsayılan proje şablonunun bileşeninin () sayaç değerini korumak için kullanılır `Counter` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="ac7b1-227">Kapsayıcıyı serileştirmek ve seri durumdan çıkarmak için yöntemleri temel alır <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="ac7b1-228">`Counter`Bileşen, değeri bileşenin dışında tutmak için durum kapsayıcısını kullanır `currentCount` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="ac7b1-229">Konumundan bir `ApplicationAuthenticationState` oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="ac7b1-230">`Id`Yerel olarak depolanmış durum için tanımlayıcı görevi gören bir özellik sağlayın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="ac7b1-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="ac7b1-232">`Authentication`Bileşen ( `Pages/Authentication.razor` ), `StateContainer` serileştirme ve seri durumdan çıkarma yöntemleriyle yerel oturum depolamayı kullanarak uygulamanın durumunu kaydeder ve geri yükler `GetStateForLocalStorage` ve `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="ac7b1-233">Bu örnek, kimlik doğrulaması için Azure Active Directory (AAD) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="ac7b1-234">İçinde `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="ac7b1-235">, `ApplicationAuthenticationState` Microsoft autentication Library (msal) türü olarak yapılandırılır `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="ac7b1-236">Durum kapsayıcısı, hizmet kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="ac7b1-237">Uygulama yollarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ac7b1-237">Customize app routes</span></span>

<span data-ttu-id="ac7b1-238">Varsayılan olarak, [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ac7b1-239">Yol</span><span class="sxs-lookup"><span data-stu-id="ac7b1-239">Route</span></span>                            | <span data-ttu-id="ac7b1-240">Amaç</span><span class="sxs-lookup"><span data-stu-id="ac7b1-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ac7b1-241">Bir oturum açma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ac7b1-242">Herhangi bir oturum açma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ac7b1-243">Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ac7b1-244">Bir oturum kapatma işlemi tetikler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ac7b1-245">Bir oturum kapatma işleminin sonucunu işler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ac7b1-246">Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ac7b1-247">Kullanıcının oturumu başarıyla sonlandırdığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ac7b1-248">Kullanıcı profilini düzenlemek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ac7b1-249">Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ac7b1-250">Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ac7b1-251">Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ac7b1-252">Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ac7b1-253">`Authentication` bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ac7b1-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-254">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="ac7b1-255">Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> açık bir eylem parametresiyle işleme yapın:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ac7b1-256">Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ac7b1-257">Kimlik doğrulama kullanıcı arabirimini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ac7b1-257">Customize the authentication user interface</span></span>

<span data-ttu-id="ac7b1-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ac7b1-259">Her durum, özel olarak geçirerek özelleştirilebilir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="ac7b1-260">İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> aşağıdaki gibi değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="ac7b1-261">`Authentication` bileşen ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="ac7b1-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="ac7b1-262">, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ac7b1-263">Yol</span><span class="sxs-lookup"><span data-stu-id="ac7b1-263">Route</span></span>                            | <span data-ttu-id="ac7b1-264">Parça</span><span class="sxs-lookup"><span data-stu-id="ac7b1-264">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="ac7b1-265">Kullanıcıyı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ac7b1-265">Customize the user</span></span>

<span data-ttu-id="ac7b1-266">Uygulamayla bağlantılı kullanıcılar özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="ac7b1-267">Kullanıcıyı bir yük talebi ile özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ac7b1-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="ac7b1-268">Aşağıdaki örnekte, uygulamanın kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="ac7b1-269">`amr`Talep, Microsoft :::no-loc(Identity)::: Platform v 1.0 [Yük taleplerinden](/azure/active-directory/develop/access-tokens#the-amr-claim)belirtecin konusunun nasıl doğrulandığını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft :::no-loc(Identity)::: Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="ac7b1-270">Örnek, temelinde özel bir kullanıcı hesabı sınıfı kullanır <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="ac7b1-271">Sınıfı genişleten bir sınıf oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="ac7b1-272">Aşağıdaki örnek, `AuthenticationMethod` özelliğini kullanıcının `amr` JSON özellik değerleri dizisine ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="ac7b1-273">`AuthenticationMethod` , kullanıcının kimliği doğrulandığında Framework tarafından otomatik olarak doldurulur.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ac7b1-274"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>Kullanıcının içinde depolanan kimlik doğrulama yöntemlerinden talepler oluşturmak için genişleten bir fabrika oluşturun `CustomUserAccount.AuthenticationMethod` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="ac7b1-275">`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ac7b1-276">Aşağıdaki kayıtlardan herhangi biri geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="ac7b1-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="ac7b1-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="ac7b1-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="ac7b1-280">Özel bir kullanıcı hesabı sınıfına sahip AAD güvenlik grupları ve rolleri</span><span class="sxs-lookup"><span data-stu-id="ac7b1-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="ac7b1-281">AAD güvenlik grupları ve AAD yönetici rolleriyle ve özel bir kullanıcı hesabı sınıfıyla birlikte çalışarak ek bir örnek için bkz <xref:blazor/security/webassembly/aad-groups-roles> ..</span><span class="sxs-lookup"><span data-stu-id="ac7b1-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ac7b1-282">Kimlik doğrulaması ile prerendering desteği</span><span class="sxs-lookup"><span data-stu-id="ac7b1-282">Support prerendering with authentication</span></span>

<span data-ttu-id="ac7b1-283">Barındırılan uygulama konularından birindeki yönergeleri uyguladıktan sonra :::no-loc(Blazor WebAssembly)::: aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-283">After following the guidance in one of the hosted :::no-loc(Blazor WebAssembly)::: app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ac7b1-284">Yetkilendirmenin gerekmediği ön ekler yolları.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ac7b1-285">Yetkilendirme gerekli olan PreRender yolları değildir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ac7b1-286">*`Client`* Uygulamanın `Program` sınıfında ( `Program.cs` ), ortak hizmet kayıtlarını ayrı bir yöntemde (örneğin,) çarpanlara ayırın `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="ac7b1-287">Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.Add:::no-loc(Razor):::Pages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="ac7b1-288">Sunucu uygulamasının `Startup.Configure` yönteminde, [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) ile değiştirin [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ac7b1-289">Sunucu uygulamasında, yoksa bir klasör oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="ac7b1-290">`_Host.cshtml`Sunucu uygulamasının klasörünün içinde bir sayfa oluşturun `Pages` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="ac7b1-291">İçeriği *`Client`* uygulamanın `wwwroot/index.html` dosyasından `Pages/_Host.cshtml` dosyasına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="ac7b1-292">Dosyanın içeriğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-292">Update the file's contents:</span></span>

* <span data-ttu-id="ac7b1-293">`@page "_Host"`Dosyanın en üstüne ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ac7b1-294">`<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-294">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ac7b1-295">Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler</span><span class="sxs-lookup"><span data-stu-id="ac7b1-295">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ac7b1-296">Bir üçüncü taraf sağlayıcı ile barındırılan bir uygulamayı kimlik doğrulaması ve yetkilendirirken :::no-loc(Blazor WebAssembly)::: , kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-296">When authenticating and authorizing a hosted :::no-loc(Blazor WebAssembly)::: app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ac7b1-297">Seçtiğiniz bir senaryo, senaryonuza bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-297">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ac7b1-298">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-298">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ac7b1-299">Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="ac7b1-299">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ac7b1-300">Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-300">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ac7b1-301">Bu senaryoda:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-301">In this scenario:</span></span>

* <span data-ttu-id="ac7b1-302">Uygulamayı barındıran sunucu bir rol oynamıyor.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-302">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ac7b1-303">Sunucudaki API 'Ler korunamaz.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-303">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ac7b1-304">Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-304">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ac7b1-305">Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma</span><span class="sxs-lookup"><span data-stu-id="ac7b1-305">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ac7b1-306">:::no-loc(Identity):::Bir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-306">Configure :::no-loc(Identity)::: with a third-party login provider.</span></span> <span data-ttu-id="ac7b1-307">Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-307">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ac7b1-308">Bir Kullanıcı oturum açtığında, :::no-loc(Identity)::: kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-308">When a user logs in, :::no-loc(Identity)::: collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ac7b1-309">Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-309">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ac7b1-310">Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın</span><span class="sxs-lookup"><span data-stu-id="ac7b1-310">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ac7b1-311">Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-311">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ac7b1-312">Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-312">From there, use the third-party access token to call third-party API resources directly from :::no-loc(Identity)::: on the client.</span></span>

<span data-ttu-id="ac7b1-313">Bu yaklaşımı önermiyoruz.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-313">We don't recommend this approach.</span></span> <span data-ttu-id="ac7b1-314">Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-314">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ac7b1-315">OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-315">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ac7b1-316">Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-316">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ac7b1-317">Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-317">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ac7b1-318">Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-318">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ac7b1-319">Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın</span><span class="sxs-lookup"><span data-stu-id="ac7b1-319">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ac7b1-320">İstemciden sunucu API 'sine bir API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-320">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ac7b1-321">Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-321">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ac7b1-322">Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-322">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ac7b1-323">Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-323">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ac7b1-324">Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-324">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="ac7b1-325">OpenID Connect (OıDC) v 2.0 uç noktalarını kullan</span><span class="sxs-lookup"><span data-stu-id="ac7b1-325">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="ac7b1-326">Kimlik doğrulama kitaplığı ve :::no-loc(Blazor)::: Proje şablonları, OpenID Connect (OıDC) v 1.0 uç noktalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-326">The authentication library and :::no-loc(Blazor)::: project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="ac7b1-327">Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-327">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="ac7b1-328">Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-328">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="ac7b1-329">Alternatif olarak, ayar uygulama ayarları ( `appsettings.json` ) dosyasında yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-329">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="ac7b1-330">Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-330">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="ac7b1-331"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>Ya da uygulama ayarları dosyasındaki ( `appsettings.json` ) özelliğini anahtarıyla ayarlayın `Authority` .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-331">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="ac7b1-332">KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-332">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="ac7b1-333">Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-333">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="ac7b1-334">Bileşenlerinde gRPC 'yi yapılandırma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="ac7b1-334">Configure and use gRPC in components</span></span>

<span data-ttu-id="ac7b1-335">:::no-loc(Blazor WebAssembly)::: [ASP.NET Core GRPC çerçevesini](xref:grpc/index)kullanmak üzere bir uygulamayı yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="ac7b1-335">To configure a :::no-loc(Blazor WebAssembly)::: app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="ac7b1-336">Sunucuda gRPC-Web ' i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-336">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="ac7b1-337">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-337">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="ac7b1-338">Uygulamanın ileti işleyicisi için gRPC hizmetlerini kaydettirin.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-338">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="ac7b1-339">Aşağıdaki örnek, uygulamanın yetkilendirme iletisi işleyicisini [ `GreeterClient` GRPC öğreticisindeki](xref:tutorials/grpc/grpc-start#create-a-grpc-service) () hizmetini kullanacak şekilde yapılandırır `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-339">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="ac7b1-340">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-340">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span> <span data-ttu-id="ac7b1-341">`.proto`Dosyayı `Shared` barındırılan çözümün projesine yerleştirin :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="ac7b1-341">Place the `.proto` file in the `Shared` project of the hosted :::no-loc(Blazor)::: solution.</span></span>

<span data-ttu-id="ac7b1-342">İstemci uygulamasındaki bir bileşen GRPC istemcisini () kullanarak gRPC çağrıları yapabilir `Pages/Grpc.razor` :</span><span class="sxs-lookup"><span data-stu-id="ac7b1-342">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="ac7b1-343">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="ac7b1-343">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span> <span data-ttu-id="ac7b1-344">Özelliğini kullanmak için `Status.DebugException` , [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) Version 2.30.0 veya üstünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-344">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="ac7b1-345">Daha fazla bilgi için bkz. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="ac7b1-345">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac7b1-346">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac7b1-346">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="ac7b1-347">`HttpClient` ve `HttpRequestMessage` Fetch API isteği seçenekleriyle</span><span class="sxs-lookup"><span data-stu-id="ac7b1-347">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
