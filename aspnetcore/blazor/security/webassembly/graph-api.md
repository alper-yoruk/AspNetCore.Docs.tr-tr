---
title: 'ASP.NET Core ile Graph API kullanma :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Graph API :::no-loc(Blazor)::: webassemlby uygulamalarıyla nasıl kullanacağınızı öğrenin.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: c346a80008b411e9fbbc584f7906227ab836d5fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055210"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="fbd3e-103">ASP.NET Core ile Graph API kullanma :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="fbd3e-103">Use Graph API with ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="fbd3e-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="fbd3e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fbd3e-105">[MICROSOFT Graph API](/graph/use-the-api) 'si, :::no-loc(Blazor)::: ve diğer .NET Framework uygulamalarının Microsoft bulut hizmet kaynaklarına erişmesini sağlayan bir yeniden takıp eden Web API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables :::no-loc(Blazor)::: and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="fbd3e-106">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="fbd3e-106">Graph SDK</span></span>

<span data-ttu-id="fbd3e-107">[Microsoft Graph SDK](/graph/sdks/sdks-overview) 'lar, Microsoft Graph erişen yüksek kaliteli, verimli ve dayanıklı uygulamalar oluşturmayı basitleştirmek üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="fbd3e-108">Bu bölümdeki örneklerde, tek başına veya uygulamanın proje dosyasının proje dosyasında aşağıdaki paketler için paket başvuruları gerekir *`Client`* :</span><span class="sxs-lookup"><span data-stu-id="fbd3e-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="fbd3e-109">Aşağıdaki yardımcı program sınıfları ve yapılandırması, bu makalenin aşağıdaki alt bölümleri için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="fbd3e-110">Graph SDK kullanarak bir bileşenden Graph API çağırma</span><span class="sxs-lookup"><span data-stu-id="fbd3e-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="fbd3e-111">Graph SDK ile Kullanıcı taleplerini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbd3e-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="fbd3e-112">Azure portal AAD alanına Microsoft Graph API kapsamları ekledikten sonra:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="fbd3e-113">Aşağıdaki `GraphClientExtensions.cs` sınıfı *`Client`* bir barındırılan çözümün tek başına uygulamasına veya uygulamasına ekleyin :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span>
* <span data-ttu-id="fbd3e-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>Yöntemi içindeki öğesinin özelliğine gerekli kapsamları sağlayın <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> `AuthenticateRequestAsync` .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="fbd3e-115">Aşağıdaki örnekte `User.Read` kapsam, bu makalenin sonraki bölümlerindeki örneklerle eşleşecek şekilde belirtilmiştir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider Provider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await Provider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient client;

        public HttpClientHttpProvider(HttpClient client)
        {
            this.client = client;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return client.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return client.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="fbd3e-116">Önceki koddaki yer tutucu, `{STRING ARRAY OF SCOPES}` izin verilen kapsamların dize dizisidir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-116">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="fbd3e-117">Örneğin, `Scopes` `User.Read` Bu makalenin aşağıdaki bölümlerindeki örneklerin kapsamına ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-117">For example, set `Scopes` to the `User.Read` scope for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="fbd3e-118">`Program.Main`( `Program.cs` ) Içinde, Graph Client Hizmetleri ve yapılandırmasını `AddGraphClient` uzantı yöntemiyle ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

<span data-ttu-id="fbd3e-119">Önceki koddaki yer tutucu, `{STRING ARRAY OF SCOPES}` izin verilen kapsamların dize dizisidir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-119">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="fbd3e-120">Örneğin, `User.Read` `AddGraphClient` Bu makalenin aşağıdaki bölümlerindeki örnekler için kapsamı öğesine geçirin:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="fbd3e-121">Graph SDK kullanarak bir bileşenden Graph API çağırma</span><span class="sxs-lookup"><span data-stu-id="fbd3e-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="fbd3e-122">Bu bölümde, bu makalede daha önce açıklanan [yardımcı program sınıfları ( `GraphClientExtensions.cs` )](#graph-sdk) kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="fbd3e-123">Aşağıdaki `GraphExample` bileşen `GraphServiceClient` , kullanıcının AAD profil verilerini elde etmek ve cep telefonu numaralarını göstermek için eklenen ' i kullanır:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="fbd3e-124">Graph SDK ile Kullanıcı taleplerini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbd3e-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="fbd3e-125">Bu bölümde, bu makalede daha önce açıklanan [yardımcı program sınıfları ( `GraphClientExtensions.cs` )](#graph-sdk) kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="fbd3e-126">Aşağıdaki örnekte, uygulama bir kullanıcı için AAD Kullanıcı profilinin cep telefonu numarasından bir cep telefonu numarası talebi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="fbd3e-127">Uygulamanın `User.Read` AAD 'de yapılandırılmış Graph API kapsamı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="fbd3e-128">Aşağıdaki özel kullanıcı hesabı fabrikasında, çerçeve <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> kullanıcının hesabını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="fbd3e-129">Uygulamanın genişlettiği özel bir kullanıcı hesabı sınıfı gerekiyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="fbd3e-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-130">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="fbd3e-131">`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="fbd3e-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="fbd3e-132">Graph API ile adlandırılmış istemci</span><span class="sxs-lookup"><span data-stu-id="fbd3e-132">Named client with Graph API</span></span>

<span data-ttu-id="fbd3e-133">Bu bölümdeki örneklerde, bir <xref:System.Net.Http.HttpClient> kullanıcının cep telefonu numarasını bir çağrıyı işleyecek şekilde almak için Graph API için adlı bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="fbd3e-134">Bu bölümdeki örneklerde, [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) tek başına veya uygulamanın proje dosyasının proje dosyasında için bir paket başvurusu gerekir *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="fbd3e-135">Graph API çalışmak için aşağıdaki sınıfı ve proje yapılandırmasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="fbd3e-136">Aşağıdaki sınıf ve yapılandırma Bu makalenin aşağıdaki alt bölümleri için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="fbd3e-137">Bir bileşenden Graph API çağırma</span><span class="sxs-lookup"><span data-stu-id="fbd3e-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="fbd3e-138">Graph API ve adlandırılmış istemciyle Kullanıcı taleplerini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbd3e-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="fbd3e-139">Azure portal AAD alanına Microsoft Graph API kapsamları ekledikten sonra, uygulamanın Graph API için yapılandırılan işleyicisine gerekli kapsamları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="fbd3e-140">Aşağıdaki örnek, kapsamı için işleyiciyi yapılandırır `User.Read` .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="fbd3e-141">Ek kapsamlar eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-141">Additional scopes can be added.</span></span>

<span data-ttu-id="fbd3e-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="fbd3e-143">`Program.Main`( `Program.cs` ) İçinde, Graph API için adlı adı yapılandırın <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="fbd3e-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="fbd3e-144">Bir bileşenden Graph API çağırma</span><span class="sxs-lookup"><span data-stu-id="fbd3e-144">Call Graph API from a component</span></span>

<span data-ttu-id="fbd3e-145">Bu bölümde, Graph API için adlandırılmış bir ad sağlayan bu makalede daha önce açıklanan [ `GraphAuthorizationMessageHandler.cs` `Program.Main` uygulama Için grafik yetkilendirme iletisi işleyicisi () ve eklemeleri](#named-client-with-graph-api) kullanılmaktadır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="fbd3e-146">Bir :::no-loc(Razor)::: bileşende:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-146">In a :::no-loc(Razor)::: component:</span></span>

* <span data-ttu-id="fbd3e-147">Bir <xref:System.Net.Http.HttpClient> Graph API oluşturun ve kullanıcının profil verileri için bir istek verin.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="fbd3e-148">`UserInfo.cs`Sınıfı, gerekli Kullanıcı profili özelliklerini <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> ÖZNITELIK ve AAD tarafından bu özellikler IÇIN kullanılan JSON adıyla belirler.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="fbd3e-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-149">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="fbd3e-150">Önceki örnekte, geliştirici özel `ICallProcessor` ( `CallProcessor` ) kuyruğuna uygular ve sonra otomatik çağrılar yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-150">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="fbd3e-151">Graph API ve adlandırılmış istemciyle Kullanıcı taleplerini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbd3e-151">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="fbd3e-152">Bu bölümde, Graph API için adlandırılmış bir ad sağlayan bu makalede daha önce açıklanan [ `GraphAuthorizationMessageHandler.cs` `Program.Main` uygulama Için grafik yetkilendirme iletisi işleyicisi () ve eklemeleri](#named-client-with-graph-api) kullanılmaktadır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-152">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="fbd3e-153">Aşağıdaki örnekte, uygulama kullanıcı için AAD Kullanıcı profilinin cep telefonu numarasından bir cep telefonu numarası talebi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-153">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="fbd3e-154">Uygulamanın `User.Read` AAD 'de yapılandırılmış Graph API kapsamı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-154">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="fbd3e-155">Uygulamaya bir `UserInfo.cs` sınıf ekleyin ve gerekli Kullanıcı profili özelliklerini, <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> Bu ÖZELLIKLER için AAD tarafından kullanılan ÖZNITELIĞIYLE ve JSON adıyla belirleyin:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-155">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="fbd3e-156">Aşağıdaki özel kullanıcı hesabı fabrikasında, çerçeve <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> kullanıcının hesabını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-156">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="fbd3e-157">Uygulamanın genişlettiği özel bir kullanıcı hesabı sınıfı gerekiyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="fbd3e-157">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="fbd3e-158">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="fbd3e-158">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="fbd3e-159">`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="fbd3e-159">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="fbd3e-160">Yukarıdaki örnek, MSAL ile AAD kimlik doğrulaması kullanan bir uygulama içindir.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-160">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="fbd3e-161">OıDC ve API kimlik doğrulaması için benzer desenler mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-161">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="fbd3e-162">Daha fazla bilgi için, [kullanıcıyı bir yük talebi Ile özelleştirme](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) bölümündeki örneklere bakın.</span><span class="sxs-lookup"><span data-stu-id="fbd3e-162">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>