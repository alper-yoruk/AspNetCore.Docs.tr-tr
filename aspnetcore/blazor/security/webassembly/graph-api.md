---
title: ASP.NET Core ile Graph API kullanma Blazor WebAssembly
author: guardrex
description: Graph API Blazor webassemlby uygulamalarıyla nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 6464b80d52837e7fe35efe5daac2193b77e21c84
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637658"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core ile Graph API kullanma Blazor WebAssembly

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

::: moniker range=">= aspnetcore-5.0"

[MICROSOFT Graph API](/graph/use-the-api) 'si, Blazor ve diğer .NET Framework uygulamalarının Microsoft bulut hizmet kaynaklarına erişmesini sağlayan bir yeniden takıp eden Web API 'sidir.

## <a name="graph-sdk"></a>Graph SDK

[Microsoft Graph SDK](/graph/sdks/sdks-overview) 'lar, Microsoft Graph erişen yüksek kaliteli, verimli ve dayanıklı uygulamalar oluşturmayı basitleştirmek üzere tasarlanmıştır.

Bu bölümdeki örneklerde, tek başına veya uygulamanın proje dosyasının proje dosyasında aşağıdaki paketler için paket başvuruları gerekir *`Client`* :

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

Aşağıdaki yardımcı program sınıfları ve yapılandırması, bu makalenin aşağıdaki alt bölümleri için kullanılır:

* [Graph SDK kullanarak bir bileşenden Graph API çağırma](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Graph SDK ile Kullanıcı taleplerini özelleştirme](#customize-user-claims-with-the-graph-sdk)

Azure portal AAD alanına Microsoft Graph API kapsamları ekledikten sonra:

* Aşağıdaki `GraphClientExtensions.cs` sınıfı *`Client`* bir barındırılan çözümün tek başına uygulamasına veya uygulamasına ekleyin Blazor .
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>Yöntemi içindeki öğesinin özelliğine gerekli kapsamları sağlayın <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> `AuthenticateRequestAsync` . Aşağıdaki örnekte `User.Read` kapsam, bu makalenin sonraki bölümlerindeki örneklerle eşleşecek şekilde belirtilmiştir.

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
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
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
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

Önceki koddaki yer tutucu, `{STRING ARRAY OF SCOPES}` izin verilen kapsamların dize dizisidir. Örneğin, `Scopes` `User.Read` Bu makalenin aşağıdaki bölümlerindeki örneklerin kapsamına ayarlayın:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

`Program.Main`( `Program.cs` ) Içinde, Graph Client Hizmetleri ve yapılandırmasını `AddGraphClient` uzantı yöntemiyle ekleyin:

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

Önceki koddaki yer tutucu, `{STRING ARRAY OF SCOPES}` izin verilen kapsamların dize dizisidir. Örneğin, `User.Read` `AddGraphClient` Bu makalenin aşağıdaki bölümlerindeki örnekler için kapsamı öğesine geçirin:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Graph SDK kullanarak bir bileşenden Graph API çağırma

Bu bölümde, bu makalede daha önce açıklanan [yardımcı program sınıfları ( `GraphClientExtensions.cs` )](#graph-sdk) kullanılmaktadır. Aşağıdaki `GraphExample` bileşen `GraphServiceClient` , kullanıcının AAD profil verilerini elde etmek ve cep telefonu numaralarını göstermek için eklenen ' i kullanır:

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

### <a name="customize-user-claims-with-the-graph-sdk"></a>Graph SDK ile Kullanıcı taleplerini özelleştirme

Bu bölümde, bu makalede daha önce açıklanan [yardımcı program sınıfları ( `GraphClientExtensions.cs` )](#graph-sdk) kullanılmaktadır.

Aşağıdaki örnekte, uygulama bir kullanıcı için AAD Kullanıcı profilinin cep telefonu numarasından bir cep telefonu numarası talebi oluşturur. Uygulamanın `User.Read` AAD 'de yapılandırılmış Graph API kapsamı olmalıdır.

Aşağıdaki özel kullanıcı hesabı fabrikasında, çerçeve <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> kullanıcının hesabını temsil eder. Uygulamanın genişlettiği özel bir kullanıcı hesabı sınıfı gerekiyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

`CustomAccountFactory.cs`:

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

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
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

`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :

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

## <a name="named-client-with-graph-api"></a>Graph API ile adlandırılmış istemci

Bu bölümdeki örneklerde, bir <xref:System.Net.Http.HttpClient> kullanıcının cep telefonu numarasını bir çağrıyı işleyecek şekilde almak için Graph API için adlı bir ad kullanılır.

Bu bölümdeki örneklerde, [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) tek başına veya uygulamanın proje dosyasının proje dosyasında için bir paket başvurusu gerekir *`Client`* .

Graph API çalışmak için aşağıdaki sınıfı ve proje yapılandırmasını oluşturun. Aşağıdaki sınıf ve yapılandırma Bu makalenin aşağıdaki alt bölümleri için kullanılır:

* [Bir bileşenden Graph API çağırma](#call-graph-api-from-a-component)
* [Graph API ve adlandırılmış istemciyle Kullanıcı taleplerini özelleştirme](#customize-user-claims-with-graph-api-and-a-named-client)

Azure portal AAD alanına Microsoft Graph API kapsamları ekledikten sonra, uygulamanın Graph API için yapılandırılan işleyicisine gerekli kapsamları sağlayın. Aşağıdaki örnek, kapsamı için işleyiciyi yapılandırır `User.Read` . Ek kapsamlar eklenebilir.

`GraphAuthorizationMessageHandler.cs`:

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

`Program.Main`( `Program.cs` ) İçinde, Graph API için adlı adı yapılandırın <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Bir bileşenden Graph API çağırma

Bu bölümde, Graph API için adlandırılmış bir ad sağlayan bu makalede daha önce açıklanan [ `GraphAuthorizationMessageHandler.cs` `Program.Main` uygulama Için grafik yetkilendirme iletisi işleyicisi () ve eklemeleri](#named-client-with-graph-api) kullanılmaktadır <xref:System.Net.Http.HttpClient> .

Bir Razor bileşende:

* Bir <xref:System.Net.Http.HttpClient> Graph API oluşturun ve kullanıcının profil verileri için bir istek verin.
* `UserInfo.cs`Sınıfı, gerekli Kullanıcı profili özelliklerini <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> ÖZNITELIK ve AAD tarafından bu özellikler IÇIN kullanılan JSON adıyla belirler.

`Pages/CallUser.razor`:

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
> Önceki örnekte, geliştirici özel `ICallProcessor` ( `CallProcessor` ) kuyruğuna uygular ve sonra otomatik çağrılar yerleştirir.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Graph API ve adlandırılmış istemciyle Kullanıcı taleplerini özelleştirme

Bu bölümde, Graph API için adlandırılmış bir ad sağlayan bu makalede daha önce açıklanan [ `GraphAuthorizationMessageHandler.cs` `Program.Main` uygulama Için grafik yetkilendirme iletisi işleyicisi () ve eklemeleri](#named-client-with-graph-api) kullanılmaktadır <xref:System.Net.Http.HttpClient> .

Aşağıdaki örnekte, uygulama kullanıcı için AAD Kullanıcı profilinin cep telefonu numarasından bir cep telefonu numarası talebi oluşturur. Uygulamanın `User.Read` AAD 'de yapılandırılmış Graph API kapsamı olmalıdır.

Uygulamaya bir `UserInfo.cs` sınıf ekleyin ve gerekli Kullanıcı profili özelliklerini, <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> Bu ÖZELLIKLER için AAD tarafından kullanılan ÖZNITELIĞIYLE ve JSON adıyla belirleyin:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

Aşağıdaki özel kullanıcı hesabı fabrikasında, çerçeve <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> kullanıcının hesabını temsil eder. Uygulamanın genişlettiği özel bir kullanıcı hesabı sınıfı gerekiyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

`CustomAccountFactory.cs`:

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

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
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

`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :

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

Yukarıdaki örnek, MSAL ile AAD kimlik doğrulaması kullanan bir uygulama içindir. OıDC ve API kimlik doğrulaması için benzer desenler mevcuttur. Daha fazla bilgi için, [kullanıcıyı bir yük talebi Ile özelleştirme](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) bölümündeki örneklere bakın.
