---
title: ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları
author: guardrex
description: Blazor WebAssemblyEk güvenlik senaryoları için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: e1f7e8b85537f0671451d9975487645a1c005e74
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626291"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

## <a name="attach-tokens-to-outgoing-requests"></a>Giden isteklere belirteç iliştirme

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> . Belirteçler, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Framework tarafından kaydedilen hizmet kullanılarak alınır. Bir belirteç alınamadığından, bir oluşturulur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>, yeni bir belirteç almak için kullanıcıdan kimlik sağlayıcısına gitmek üzere kullanılabilecek bir yönteme sahiptir.

Kolaylık olması için Framework, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> uygulamanın temel adresiyle önceden yapılandırılmış bir yetkili URL 'si sağlar. **Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.** Giden istek URI 'Leri uygulamanın temel URI 'SI içinde olmadığında, [özel bir sınıf kullanın `AuthorizationMessageHandler` (*önerilir*)](#custom-authorizationmessagehandler-class) veya öğesini [yapılandırın `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).

> [!NOTE]
> Sunucu API 'SI erişimi için istemci uygulama yapılandırmasına ek olarak, istemci ve sunucu aynı temel adreste olmadığında sunucu API 'SI de çıkış noktaları arası isteklere (CORS) izin vermelidir. Sunucu tarafı CORS yapılandırması hakkında daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [çıkış noktaları arası kaynak paylaşımı (CORS)](#cross-origin-resource-sharing-cors) bölümüne bakın.

Aşağıdaki örnekte:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A><xref:System.Net.Http.IHttpClientFactory>hizmet koleksiyonuna ve ilgili hizmetleri ekler ve adlandırılmış bir <xref:System.Net.Http.HttpClient> ( `ServerAPI` ) yapılandırır. <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> , istek gönderilirken Kaynak URI 'sinin temel adresidir. <xref:System.Net.Http.IHttpClientFactory> , [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet paketi tarafından sağlanır.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> , <xref:System.Net.Http.DelegatingHandler> giden örneklere erişim belirteçleri eklemek için kullanılır <xref:System.Net.Http.HttpResponseMessage> . Erişim belirteçleri yalnızca istek URI 'si uygulamanın temel URI 'SI içinde olduğunda eklenir.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType><xref:System.Net.Http.HttpClient>adlandırılmış () öğesine karşılık gelen yapılandırmayı kullanarak giden istekler için bir örnek oluşturur ve yapılandırır <xref:System.Net.Http.HttpClient> `ServerAPI` .

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

BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , Istek URI 'leri varsayılan olarak uygulamanın temel URI 'si içindedir. Bu nedenle, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Proje şablonundan oluşturulan bir uygulamada öğesine atanır.

Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) :

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

### <a name="custom-authorizationmessagehandler-class"></a>Özel `AuthorizationMessageHandler` sınıf

*Bu bölümdeki kılavuz, uygulamanın temel URI 'SI içinde olmayan URI 'lere giden istekleri yapan istemci uygulamalar için önerilir.*

Aşağıdaki örnekte, için bir özel sınıf <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> için olarak kullanılmak üzere genişletilir <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Bu işleyiciyi, bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek üzere yapılandırır. Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> .

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

`Program.Main`( `Program.cs` ) İçinde, `CustomAuthorizationMessageHandler` kapsamlı bir hizmet olarak kaydedilir ve <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpResponseMessage> bir adlandırılmış tarafından yapılan giden örnekler için olarak yapılandırılır <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.

Yapılandırma, <xref:System.Net.Http.HttpClient> model kullanarak yetkili istekler oluşturmak için kullanılır [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) . İstemcisinin <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) paket) oluşturulduğu yerde, <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlanır. İstek URI 'si, aşağıdaki örnekte olduğu gibi göreli bir URI ise, `ExampleAPIMethod` <xref:System.Net.Http.HttpClient.BaseAddress> istemci uygulaması isteği yaptığında ile birleştirilir:

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

### <a name="configure-authorizationmessagehandler"></a>Yapılandırma `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , yöntemi kullanılarak yetkili URL 'Ler, kapsamlar ve dönüş URL 'SI ile yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> , bir erişim belirteci kullanarak giden HTTP isteklerini yetkilendirmek için işleyiciyi yapılandırır. Erişim belirteci yalnızca, yetkili URL 'Lerden en az biri istek URI 'sinin () bir taban ise eklenir <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> . İstek URI 'si göreli bir URI ise, ile birleştirilir <xref:System.Net.Http.HttpClient.BaseAddress> .

Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> () bir içinde yapılandırır `Program.Main` `Program.cs` :

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

BlazorBarındırılan proje şablonunu temel alan bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Dizinin URL 'SI `authorizedUrls` .

## <a name="typed-httpclient"></a>Girdiyseniz `HttpClient`

Türü belirtilmiş bir istemci, tüm HTTP ve Token Alım sorunlarını tek bir sınıf içinde işleyen tanımlanabilir.

`WeatherForecastClient.cs`:

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

Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `using static BlazorSample.Data;` ).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.

`FetchData` bileşen ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>İşleyiciyi yapılandırma `HttpClient`

İşleyici, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> gıden http istekleri için ile daha da yapılandırılabilir.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

BlazorBarındırılan proje şablonunu temel alan bir uygulama için Blazor WebAssembly <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> Varsayılan olarak aşağıdakilere atanır:

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Dizinin URL 'SI `authorizedUrls` .

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri

Blazor WebAssemblyUygulama genellikle güvenli bir varsayılan değer kullanıyorsa <xref:System.Net.Http.HttpClient> , uygulama adlandırılmış bir yapılandırma yaparak kimliği doğrulanmamış veya YETKILENDIRILMEMIŞ Web API istekleri de yapabilir <xref:System.Net.Http.HttpClient> .

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

BlazorBarındırılan proje şablonuna dayalı bir uygulama için Blazor WebAssembly , <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak öğesine atanır.

Önceki kayıt, var olan güvenli varsayılan kayda ek niteliğindedir <xref:System.Net.Http.HttpClient> .

Bir bileşen, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) kimliği doğrulanmamış veya yetkisiz istekler yapmak için (paket) öğesinden öğesini oluşturur:

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
> Önceki örnek için sunucu API 'sindeki denetleyici, `WeatherForecastNoAuthenticationController` [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğiyle işaretlenmez.

Varsayılan örnek, geliştiriciye yönelik olan güvenli istemci veya güvenli olmayan bir istemci kullanılıp kullanılmayacağını <xref:System.Net.Http.HttpClient> karardır. Bu kararı yapmanın bir yolu, uygulamanın ilgili olduğu kimliği doğrulanmamış ve kimliği doğrulanmamış uç noktaların sayısını göz önünde bulundurmanız. Uygulama isteklerinin çoğunluğu API uç noktalarına güvense, <xref:System.Net.Http.HttpClient> Varsayılan olarak kimliği doğrulanmış örneği kullanın. Aksi takdirde, kimliği doğrulanmamış <xref:System.Net.Http.HttpClient> örneği varsayılan olarak kaydedin.

' Nin kullanılmasına alternatif bir yaklaşım, <xref:System.Net.Http.IHttpClientFactory> anonim uç noktalara kimliği doğrulanmamış erişim için [yazılmış bir istemci](#typed-httpclient) oluşturmaktır.

## <a name="request-additional-access-tokens"></a>Ek erişim belirteçleri isteyin

Erişim belirteçleri, çağırarak el ile elde edilebilir `IAccessTokenProvider.RequestAccessToken` .

Aşağıdaki örnekte, Kullanıcı verilerini okumak ve posta göndermek için bir uygulama tarafından ek Azure Active Directory (AAD) Microsoft Graph API kapsamları gerekir. Azure AAD portalında Microsoft Graph API izinleri eklendikten sonra, Istemci uygulamasında ek kapsamlar yapılandırılır.

`Program.Main` (`Program.cs`):

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

`IAccessTokenProvider.RequestToken`Yöntemi, bir uygulamanın belirli bir kapsam kümesiyle bir erişim belirteci sağlamasını sağlayan bir aşırı yükleme sağlar.

Bir Razor bileşende:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> döndürdüğü

* `true` ' i `token` kullanın.
* `false` belirteç alınmadıysa.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` ve `HttpRequestMessage` Fetch API isteği seçenekleriyle

Bir uygulamada WebAssembly üzerinde çalışırken Blazor WebAssembly [`HttpClient`](xref:fundamentals/http-requests) ([API belgeleri](xref:System.Net.Http.HttpClient)) ve <xref:System.Net.Http.HttpRequestMessage> istekleri özelleştirmek için kullanılabilir. Örneğin, HTTP yöntemini ve istek üst bilgilerini belirtebilirsiniz. Aşağıdaki bileşen, `POST` sunucuda Yapılacaklar LISTESI API uç noktası için bir istek yapar ve yanıt gövdesini gösterir:

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

.NET WebAssembly 'ın uygulanması, <xref:System.Net.Http.HttpClient> [Windoworworkerglobalscope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)kullanır. Getirme, [isteğe özgü birkaç seçeneği](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)yapılandırmaya olanak tanır. 

HTTP getirme isteği seçenekleri <xref:System.Net.Http.HttpRequestMessage> , aşağıdaki tabloda gösterilen uzantı yöntemleriyle yapılandırılabilir.

| Genişletme yöntemi | Fetch isteği özelliği |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Daha genel genişletme yöntemini kullanarak ek seçenekler ayarlayabilirsiniz <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .
 
HTTP yanıtı, Blazor WebAssembly yanıt içeriği üzerinde eşitleme okuma desteğini etkinleştirmek için genellikle bir uygulamada arabelleğe kaydedilir. Yanıt akışı desteğini etkinleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> istekteki genişletme yöntemini kullanın.

Kimlik bilgilerini bir çapraz kaynak isteğine dahil etmek için, <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> genişletme yöntemini kullanın:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

CORS isteklerindeki kimlik bilgileri (yetkilendirme cookie s/üst bilgileri) gönderilirken, bir `Authorization` üst bilgiye CORS ilkesi tarafından izin verilmelidir.

Aşağıdaki ilke için yapılandırma içerir:

* İstek kaynakları ( `http://localhost:5000` , `https://localhost:5001` ).
* Any yöntemi (fiil).
* `Content-Type` ve `Authorization` üst bilgiler. Özel bir üstbilgiye (örneğin,) izin vermek için `x-custom-header` , çağrılırken üstbilgiyi listeleyin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri ( `credentials` özellik olarak ayarlanır `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

BlazorBarındırılan proje şablonunu temel alan barındırılan bir çözüm, Blazor istemci ve sunucu uygulamaları için aynı temel adresi kullanır. İstemci uygulaması <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> Varsayılan olarak BIR URI 'sine ayarlanır `builder.HostEnvironment.BaseAddress` . CORS yapılandırması, barındırılan proje şablonundan oluşturulan barındırılan bir uygulamanın varsayılan yapılandırmasında gerekli **değildir** Blazor . Sunucu projesi tarafından barındırılmayan ve sunucu uygulamasının **temel adresini** paylaşmayan ek istemci uygulamaları, sunucu projesinde CORS yapılandırması gerektirmez.

Daha fazla bilgi için bkz <xref:security/cors> . ve örnek UYGULAMANıN http Isteği Sınayıcısı bileşeni ( `Components/HTTPRequestTester.razor` ).

## <a name="handle-token-request-errors"></a>Tanıtıcı belirteci isteği hataları

Tek sayfalı uygulama (SPA), OpenID Connect (OıDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu yerel olarak SPA 'da ve Identity sağlayıcıda (IP), cookie kimlik bilgilerini sağlayan kullanıcının bir sonucu olarak ayarlanmış bir oturum biçiminde tutulur.

IP 'nin Kullanıcı için yaydığı belirteçler genellikle kısa süreler boyunca geçerlidir. bu nedenle, istemci uygulamanın düzenli olarak yeni belirteçler getirmesi gerekir. Aksi takdirde, Kullanıcı, verilen belirteçlerin süre dolduktan sonra günlüğe kaydedilir. Çoğu durumda, OıDC istemcileri kullanıcının kimlik doğrulaması durumunda veya IP içinde tutulan "oturum" için yeniden kimlik doğrulamasından geçmesini gerektirmeden yeni belirteçler sağlayabiliyor.

İstemcinin kullanıcı etkileşimi olmadan bir belirteç edinmedikleri bazı durumlar vardır, örneğin, kullanıcının IP 'den açık bir şekilde oturumu açtığı bir nedenden dolayı. Bu senaryo, bir kullanıcı tarafından ziyaret ettiğinde `https://login.microsoftonline.com` ve oturumu kapattığında oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığı hemen haberdar değildir. İstemcinin tuttuğu belirteç artık geçerli olmayabilir. Ayrıca, istemci, geçerli belirtecin süresi dolduktan sonra Kullanıcı etkileşimi olmadan yeni bir belirteç sağlayamaz.

Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir. Bunlar, maça doğaları 'nın bir parçasıdır. cookieKimlik doğrulaması kaldırılırsa, s kullanan BIR Spa da sunucu API 'sini çağıramaz cookie .

Bir uygulama, korumalı kaynaklara yönelik API çağrıları gerçekleştirdiğinde, aşağıdakilerin farkında olmanız gerekir:

* API 'yi çağırmak için yeni bir erişim belirteci sağlamak üzere kullanıcının yeniden kimlik doğrulaması yapması gerekebilir.
* İstemcinin geçerli gibi görünen bir belirteci olsa da, belirteç Kullanıcı tarafından iptal edildiğinden sunucu çağrısı başarısız olabilir.

Uygulama bir belirteç istediğinde, olası iki sonuç vardır:

* İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.
* İstek başarısız olur ve yeni bir belirteç almak için uygulamanın kullanıcının kimliğini doğrulaması gerekir.

Bir belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğinize karar vermeniz gerekir. Artan karmaşıklık düzeylerinde birkaç yaklaşım mevcuttur:

* Geçerli sayfa durumunu oturum depolama alanında depolayın. [ `OnInitializedAsync` Yaşam döngüsü olayı](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) sırasında, devam etmeden önce durumun geri yüklenebildiğinden emin olun.
* Bir sorgu dizesi parametresi ekleyin ve bunu, uygulamayı daha önce kaydedilen durumu yeniden doldurma ihtiyacı olduğunu bildirmek için bir yol olarak kullanın.
* Bir sorgu dizesi parametresini, verileri oturum depolamada diğer öğelerle riskli olmayan bir şekilde depolamak için benzersiz bir tanımlayıcıya ekleyin.

Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

* Oturum açma sayfasına yönlendirmeden önce durumu koru.
* Önceki durumu daha sonra sorgu dizesi parametresini kullanarak kimlik doğrulamasını kurtarın.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet

Bir kimlik doğrulama işlemi sırasında, tarayıcı IP 'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır. Bu durum kapsayıcısını kullanırken ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde bu durum oluşabilir. Uygulamaya özgü durumu veya bir başvuruyu korumak ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yüklemek için özel bir kimlik doğrulama durumu nesnesi kullanabilirsiniz. Aşağıdaki örnekte yaklaşım gösterilmektedir.

Uygulamanın durum değerlerini tutacak özelliklerle birlikte uygulamada bir durum kapsayıcı sınıfı oluşturulur. Aşağıdaki örnekte kapsayıcı, varsayılan proje şablonunun bileşeninin () sayaç değerini korumak için kullanılır `Counter` `Pages/Counter.razor` . Kapsayıcıyı serileştirmek ve seri durumdan çıkarmak için yöntemleri temel alır <xref:System.Text.Json> .

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

`Counter`Bileşen, değeri bileşenin dışında tutmak için durum kapsayıcısını kullanır `currentCount` :

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

Konumundan bir `ApplicationAuthenticationState` oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . `Id`Yerel olarak depolanmış durum için tanımlayıcı görevi gören bir özellik sağlayın.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication`Bileşen ( `Pages/Authentication.razor` ), `StateContainer` serileştirme ve seri durumdan çıkarma yöntemleriyle yerel oturum depolamayı kullanarak uygulamanın durumunu kaydeder ve geri yükler `GetStateForLocalStorage` ve `SetStateFromLocalStorage` :

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

Bu örnek, kimlik doğrulaması için Azure Active Directory (AAD) kullanır. İçinde `Program.Main` ( `Program.cs` ):

* , `ApplicationAuthenticationState` Microsoft autentication Library (msal) türü olarak yapılandırılır `RemoteAuthenticationState` .
* Durum kapsayıcısı, hizmet kapsayıcısına kaydedilir.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Uygulama yollarını özelleştirme

Varsayılan olarak, [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) kitaplık, farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.

| Yol                            | Amaç |
| -------------------------------- | ------- |
| `authentication/login`           | Bir oturum açma işlemini tetikler. |
| `authentication/login-callback`  | Herhangi bir oturum açma işleminin sonucunu işler. |
| `authentication/login-failed`    | Bazı nedenlerle oturum açma işlemi başarısız olduğunda hata iletilerini görüntüler. |
| `authentication/logout`          | Bir oturum kapatma işlemi tetikler. |
| `authentication/logout-callback` | Bir oturum kapatma işleminin sonucunu işler. |
| `authentication/logout-failed`   | Bir nedenden dolayı oturum kapatma işlemi başarısız olduğunda hata iletilerini görüntüler. |
| `authentication/logged-out`      | Kullanıcının oturumu başarıyla sonlandırdığını gösterir. |
| `authentication/profile`         | Kullanıcı profilini düzenlemek için bir işlem tetikler. |
| `authentication/register`        | Yeni bir kullanıcıyı kaydetmek için bir işlem tetikler. |

Yukarıdaki tabloda gösterilen rotalar aracılığıyla yapılandırılabilir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . Özel yollar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir yolu olduğunu doğrulayın.

Aşağıdaki örnekte, tüm yollar ön ekine sahiptir `/security` .

`Authentication` bileşen ( `Pages/Authentication.razor` ):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Gereksinim tamamen farklı yollar çağırırsa, daha önce açıklandığı gibi yolları ayarlayın ve öğesini <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> açık bir eylem parametresiyle işleme yapın:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Bunu seçerseniz, Kullanıcı arabirimini farklı sayfalara bölmek için izin verilir.

## <a name="customize-the-authentication-user-interface"></a>Kimlik doğrulama kullanıcı arabirimini özelleştirme

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Her kimlik doğrulama durumu için varsayılan bir UI parçaları kümesi içerir. Her durum, özel olarak geçirerek özelleştirilebilir <xref:Microsoft.AspNetCore.Components.RenderFragment> . İlk oturum açma işlemi sırasında görüntülenecek metni özelleştirmek için <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> aşağıdaki gibi değiştirebilir.

`Authentication` bileşen ( `Pages/Authentication.razor` ):

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

, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> Aşağıdaki tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçaya sahiptir.

| Yol                            | Parça                |
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

## <a name="customize-the-user"></a>Kullanıcıyı özelleştirme

Uygulamayla bağlantılı kullanıcılar özelleştirilebilir. Aşağıdaki örnekte, tüm kimliği doğrulanmış kullanıcılar `amr` kullanıcının kimlik doğrulama yöntemlerinin her biri için bir talep alır.

Sınıfı genişleten bir sınıf oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Şunu genişleten bir fabrika oluşturun <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :

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

`CustomAccountFactory`Kullanılan kimlik doğrulama sağlayıcısına kaydolun. Aşağıdaki kayıtlardan herhangi biri geçerlidir: 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

## <a name="support-prerendering-with-authentication"></a>Kimlik doğrulaması ile prerendering desteği

Barındırılan uygulama konularından birindeki yönergeleri uyguladıktan sonra Blazor WebAssembly aşağıdaki yönergeleri kullanarak şunları içeren bir uygulama oluşturun:

* Yetkilendirmenin gerekmediği ön ekler yolları.
* Yetkilendirme gerekli olan PreRender yolları değildir.

Istemci uygulamasının `Program` sınıfında ( `Program.cs` ), ortak hizmet kayıtlarını ayrı bir yönteme (örneğin,) göre çarpanlara koyun `ConfigureCommonServices` :

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

Sunucu uygulamasında, `Startup.ConfigureServices` aşağıdaki ek hizmetleri kaydedin:

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

Sunucu uygulamasının `Startup.Configure` yönteminde, [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) ile değiştirin [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Sunucu uygulamasında, yoksa bir klasör oluşturun `Pages` . `_Host.cshtml`Sunucu uygulamasının klasörünün içinde bir sayfa oluşturun `Pages` . İçeriği Istemci uygulamasının `wwwroot/index.html` dosyasından `Pages/_Host.cshtml` dosyasına yapıştırın. Dosyanın içeriğini güncelleştirin:

* `@page "_Host"`Dosyanın en üstüne ekleyin.
* `<app>Loading...</app>`Etiketi aşağıdaki kodla değiştirin:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Barındırılan uygulamalar ve üçüncü taraf oturum açma sağlayıcıları için seçenekler

Bir üçüncü taraf sağlayıcı ile barındırılan bir uygulamayı kimlik doğrulaması ve yetkilendirirken Blazor WebAssembly , kullanıcının kimliğini doğrulamak için kullanabileceğiniz çeşitli seçenekler vardır. Seçtiğiniz bir senaryo, senaryonuza bağlıdır.

Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Yalnızca korumalı üçüncü taraf API 'Leri çağırmak için kullanıcıların kimliğini doğrulama

Üçüncü taraf API sağlayıcısına karşı, istemci tarafı OAuth akışı ile kullanıcının kimliğini doğrulayın:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Bu senaryoda:

* Uygulamayı barındıran sunucu bir rol oynamıyor.
* Sunucudaki API 'Ler korunamaz.
* Uygulama yalnızca korunan üçüncü taraf API 'Leri çağırabilir.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Bir üçüncü taraf sağlayıcı ile kullanıcıların kimliğini doğrulama ve konak sunucusunda ve üçüncü taraftan korunan API 'Leri çağırma

IdentityBir üçüncü taraf oturum açma sağlayıcısıyla yapılandırın. Üçüncü taraf API erişimi için gereken belirteçleri edinin ve bunları depolayın.

Bir Kullanıcı oturum açtığında, Identity kimlik doğrulama işleminin bir parçası olarak erişimi toplar ve belirteçleri yenileyin. Bu noktada, üçüncü taraf API 'lere yönelik API çağrıları yapmak için kullanabileceğiniz birkaç yaklaşım vardır.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Üçüncü taraf erişim belirtecini almak için bir sunucu erişim belirteci kullanın

Sunucu API uç noktasından üçüncü taraf erişim belirtecini almak için sunucuda oluşturulan erişim belirtecini kullanın. Buradan, üçüncü taraf API kaynaklarını doğrudan istemcide çağırmak için üçüncü taraf erişim belirtecini kullanın Identity .

Bu yaklaşımı önermiyoruz. Bu yaklaşım, üçüncü taraf erişim belirtecinin ortak bir istemci için oluşturulmuş gibi davranılması gerektirir. OAuth koşullarında, genel uygulamanın gizli dizileri güvenli bir şekilde depolamak için güvenli hale gelmediği için bir istemci parolası yoktur ve erişim belirteci gizli bir istemci için oluşturulur. Gizli bir istemci, bir istemci gizli anahtarı olan bir istemcsahiptir ve gizli dizileri güvenli bir şekilde depolayabilecek varsayılır.

* Üçüncü taraf erişim belirtecine, üçüncü tarafın daha güvenilir bir istemcinin belirtecini kendine yayıldığından emin olmak için, hassas işlemleri gerçekleştirmek üzere ek kapsamlar verilebilir.
* Benzer şekilde, yenileme belirteçleri güvenilir olmayan bir istemciye verilmemelidir, aksi takdirde başka kısıtlamalar yerleştirilmediği sürece istemciye sınırsız erişim sağlar.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Üçüncü taraf API 'Leri çağırmak için istemciden sunucu API 'sine API çağrıları yapın

İstemciden sunucu API 'sine bir API çağrısı yapın. Sunucudan, üçüncü taraf API kaynağı için erişim belirtecini alın ve hangi çağrının gerekli olduğunu sorun.

Bu yaklaşım, bir üçüncü taraf API çağrısı yapmak için sunucu aracılığıyla fazladan bir ağ atlaması gerektirdiğinden, sonunda daha güvenli bir deneyim oluşur:

* Sunucu yenileme belirteçlerini saklayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimi kaybetmemesini sağlayabilir.
* Uygulama, daha hassas izinler içerebilen sunucudan erişim belirteçlerini sızıntısına neden olabilir.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>OpenID Connect (OıDC) v 2.0 uç noktalarını kullan

Kimlik doğrulama kitaplığı ve Blazor Proje şablonları, OpenID Connect (OıDC) v 1.0 uç noktalarını kullanır. Bir v 2.0 uç noktası kullanmak için, JWT taşıyıcı <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> seçeneğini yapılandırın. Aşağıdaki örnekte,, özelliğine bir segment eklenerek AAD, v 2.0 için yapılandırılmıştır `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> :

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Alternatif olarak, ayar uygulama ayarları ( `appsettings.json` ) dosyasında yapılabilir:

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Bir kesimdeki bir kesimde yer alan herhangi bir uygulama, AAD olmayan sağlayıcılar gibi, uygulamanın OıDC sağlayıcısına uygun değilse, <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> özelliği doğrudan ayarlayın. <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>Ya da uygulama ayarları dosyasındaki ( `appsettings.json` ) özelliğini anahtarıyla ayarlayın `Authority` .

KIMLIK belirtecindeki talepler listesi v 2.0 uç noktaları için değişir. Daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesi nedir?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Bileşenlerinde gRPC 'yi yapılandırma ve kullanma

Blazor WebAssembly [ASP.NET Core GRPC çerçevesini](xref:grpc/index)kullanmak üzere bir uygulamayı yapılandırmak için:

* Sunucuda gRPC-Web ' i etkinleştirin. Daha fazla bilgi için bkz. <xref:grpc/browser>.
* Uygulamanın ileti işleyicisi için gRPC hizmetlerini kaydettirin. Aşağıdaki örnek, uygulamanın yetkilendirme iletisi işleyicisini [ `GreeterClient` GRPC öğreticisindeki](xref:tutorials/grpc/grpc-start#create-a-grpc-service) () hizmetini kullanacak şekilde yapılandırır `Program.Main` :

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

Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ). `.proto`Dosyayı `Shared` barındırılan çözümün projesine yerleştirin Blazor .

İstemci uygulamasındaki bir bileşen GRPC istemcisini () kullanarak gRPC çağrıları yapabilir `Pages/Grpc.razor` :

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

Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample` ). Özelliğini kullanmak için `Status.DebugException` , [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) Version 2.30.0 veya üstünü kullanın.

Daha fazla bilgi için bkz. <xref:grpc/browser>.
