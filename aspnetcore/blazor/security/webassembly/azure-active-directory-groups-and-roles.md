---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a56f03f2c3acd08b009673ef7533186bf703604
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690469"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Azure Active Directory (AAD) grupları, yönetici rolleri ve Kullanıcı tanımlı roller

Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD), ile birleştirilebilir çeşitli yetkilendirme yaklaşımları sağlar ASP.NET Core Identity :

* Kullanıcı tanımlı gruplar
  * Güvenlik
  * Microsoft 365
  * Dağıtım
* Roller
  * AAD yönetici rolleri
  * Kullanıcı tanımlı roller

Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:

* [Microsoft Hesapları ile bağımsız](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD ile bağımsız](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD ile barındırılan](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a>Kapsamlar

5 ' ten fazla AAD yönetici rolü ve güvenlik grubu üyeliğine sahip tüm uygulama kullanıcıları için [MICROSOFT Graph API](/graph/use-the-api) çağrısı gerekir.

Graph API çağrılarına izin vermek için, *`Client`* Azure Portal bir barındırılan çözümün tek başına veya uygulamasına Blazor Şu [Graph API izinlerinden (kapsamlar)](/graph/permissions-reference) sahip olduğunu verin:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` en az ayrıcalıklı kapsamdır ve bu makalede açıklanan örnek için kullanılan kapsamdır.

## <a name="user-defined-groups-and-administrator-roles"></a>Kullanıcı tanımlı gruplar ve yönetici rolleri

Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın. Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve AAD yönetici rollerine atayın.

* [Azure AD güvenlik gruplarını kullanan roller](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` özniteliğe](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Aşağıdaki örneklerde, bir kullanıcının AAD *faturalandırma Yöneticisi* rolüne atandığını varsayılır.

`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar. Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.

Atanan AAD Yönetici rollerinin ve Kullanıcı tanımlı grupların sayısı beş aştığında AAD, `hasgroups` `true` talep göndermek yerine değeri olan bir talep gönderir `groups` . En fazla beş rol ve gruba atanmış olan herhangi bir uygulama, bir kullanıcının rollerini ve gruplarını almak için ayrı bir Graph API çağrısı yapmalıdır. Bu makalede belirtilen örnek uygulama, bu senaryoyu ele alınmaktadır. Daha fazla bilgi için bkz `groups` `hasgroups` . [Microsoft Identity platform erişim belirteçleri](/azure/active-directory/develop/access-tokens#payload-claims) 'nde ve talep bilgileri: yük talepleri makalesi.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Gruplar ve roller için dizi özelliklerini içerecek şekilde genişletin. `null`Bu özellikler `foreach` daha sonra Döngülerde kullanıldığında gerekli olmaması için her özelliğe boş bir dizi atayın.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

AAD grupları ve rolleri için talepler oluşturmak için aşağıdaki yaklaşımlardan **birini** kullanın:

* [Graph SDK 'sını kullanma](#use-the-graph-sdk)
* [Adlandırılmış bir ad kullanın `HttpClient`](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a>Graph SDK 'sını kullanma

İçin bir barındırılan çözümün tek başına uygulamasına veya uygulamasına bir paket başvurusu ekleyin *`Client`* Blazor [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Graph SDK yardımcı program sınıflarını ve yapılandırmasını makalenin *Graph SDK* bölümüne ekleyin <xref:blazor/security/webassembly/graph-api#graph-sdk> .

Aşağıdaki özel kullanıcı hesabı fabrikasını bir barındırılan çözümün tek başına appo veya *`Client`* uygulamasına ekleyin Blazor ( `CustomAccountFactory.cs` ). Özel Kullanıcı fabrikası, rol ve grup taleplerini işlemek için kullanılır. `roles`Talep dizisi, [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınmıştır. Talep varsa `hasgroups` , kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak üzere Graf SDK 'sı kullanılır:

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
    : AccountClaimsPrincipalFactory<CustomUserAccount>
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
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Yukarıdaki kod geçişli üyelikleri içermez. Uygulama, doğrudan ve geçişli grup üyeliği talepleri gerektiriyorsa:

* `IUserMemberOfCollectionWithReferencesPage`Türünü `groupsAndAzureRoles` olarak değiştirin `IUserTransitiveMemberOfCollectionWithReferencesPage` .
* Kullanıcının grupları ve rolleri istendiğinde, `MemberOf` özelliğini ile değiştirin `TransitiveMemberOf` .

`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a>Adlandırılmış bir ad kullanın `HttpClient`

::: moniker-end

Tek başına uygulamada veya *`Client`* barındırılan bir Blazor çözümün uygulamasında özel bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> sınıf oluşturun. Rol ve grup bilgilerini elde eden Graph API çağrıları için doğru kapsamı kullanın.

`GraphAPIAuthorizationMessageHandler.cs`:

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
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

`Program.Main`( `Program.cs` ) İçinde, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> uygulama hizmetini ekleyin ve <xref:System.Net.Http.HttpClient> Graph API istekleri yapmak için adlandırılmış bir ad ekleyin. Aşağıdaki örnekte istemcinin adı verilmiştir `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Bir Graph API çağrısından açık veri Protokolü (OData) rollerini ve gruplarını almak için AAD dizin nesneleri sınıfları oluşturun. OData JSON biçimine ulaştı ve <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> sınıfının bir örneğini dolduran bir çağrı `DirectoryObjects` .

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Rolleri ve grup taleplerini işlemek için özel bir Kullanıcı fabrikası oluşturun. Aşağıdaki örnek uygulama, `roles` [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınan talep dizisini de işler. Talep varsa `hasgroups` , <xref:System.Net.Http.HttpClient> kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak için adı kullanılır. Bu uygulama, Microsoft Identity Platform v 1.0 uç noktasını `https://graph.microsoft.com/v1.0/me/memberOf` ([API belgeleri](/graph/api/user-list-memberof)) kullanır.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Varsa, özgün talebi kaldırmak için kod sağlamanız gerekmez, çünkü varsa, `groups` Framework tarafından otomatik olarak kaldırılır.

> [!NOTE]
> Bu örnekteki yaklaşım:
>
> * <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Giden isteklere erişim belirteçleri eklemek için özel bir sınıf ekler.
> * <xref:System.Net.Http.HttpClient>Güvenli, dış Web API uç noktası için Web API istekleri yapmak üzere bir adlandırılmış adı ekler.
> * , <xref:System.Net.Http.HttpClient> Yetkili istekleri yapmak için adını kullanır.
>
> Bu yaklaşım için genel kapsam <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> makalesinde bulabilirsiniz.

`Program.Main` `Program.cs` Bir barındırılan çözümün tek başına uygulamasının veya uygulamasının fabrikasını () kaydedin *`Client`* Blazor . `Directory.Read.All`Uygulama için ek kapsam olarak kapsama onay:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a>Yetkilendirme yapılandırması

İçindeki her grup veya rol için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` . Aşağıdaki örnek AAD *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD rolü nesne kimliklerinin tamamı listesi için bkz. [AAD yönetici rolü nesne kimlikleri](#aad-administrator-role-object-ids) bölümü.

Aşağıdaki örneklerde, uygulama kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.

[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

Bir bileşenin tamamına erişim, [ `[Authorize]` öznitelik yönergesini](xref:blazor/security/index#authorize-attribute) () kullanarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.

Bir ilke denetimi, [yordamsal mantığa kodunda de gerçekleştirilebilir](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Kullanıcı tanımlı gruplar ve yönetici rolleri için sunucu API 'SI erişimini yetkilendir

Sunucu API 'SI, istemci tarafı WebAssembly uygulamasındaki sayfalara ve kaynaklara erişmek için yetkilendirmeye ek olarak, kullanıcılara güvenli API uç noktalarına erişim yetkisi verebilir. *Sunucu* uygulaması kullanıcının erişim belirtecini doğruladıktan sonra:

* Sunucu API 'SI uygulaması, Graph API için bir erişim belirteci almak için, kullanıcının kendi erişim belirtecinden sabit [nesne tanımlayıcı talebini ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) kullanır.
* Bir Graph API çağrısı kullanıcının Kullanıcı tarafından tanımlanan Azure güvenlik grubunu ve yönetici rolü üyeliklerini, kullanıcıyı çağırarak edinir [`memberOf`](/graph/api/user-list-memberof) .
* Üyelikler, talepler oluşturmak için kullanılır `group` .
* [Yetkilendirme İlkeleri](xref:security/authorization/policies) , uygulama genelınde sunucu API uç noktalarına Kullanıcı erişimini sınırlamak için kullanılabilir.

> [!NOTE]
> Bu kılavuz Şu anda kullanıcıları [AAD Kullanıcı tanımlı rollerinin](#user-defined-roles)temelinde yetkilendirmeye dahil değildir.

Bu bölümdeki kılavuz, sunucu API uygulamasını Microsoft Graph API çağrısı için bir [*Daemon uygulaması*](/azure/active-directory/develop/scenario-daemon-overview) olarak yapılandırır. Bu yaklaşım şu **değildir** :

* `access_as_user`Kapsam gerektir.
* API isteğini yapan kullanıcı/istemci adına Graph API erişin.

Sunucu API 'SI uygulaması tarafından Graph API çağrısı yalnızca Azure portal için bir sunucu API uygulama **uygulaması** Graph API kapsamı gerektirir `Directory.Read.All` . Bu yaklaşım, istemci uygulamanın, sunucu API 'sinin açıkça izin vermediği dizin verilerine erişmesini kesinlikle önler. İstemci uygulaması yalnızca sunucu API 'SI uygulamasının denetleyici uç noktalarına erişebilir.

### <a name="azure-configuration"></a>Azure yapılandırması

* *Sunucu* **uygulaması kaydına** , **Delegated** `Directory.Read.All` güvenlik grupları için en az ayrıcalıklı erişim düzeyi olan, için Graph API kapsam (temsilci yok) verildiğini doğrulayın. Kapsam atamasını yaptıktan sonra yönetim izninin kapsama uygulandığını doğrulayın.
* *Sunucu* uygulamasına yeni bir istemci parolası atayın. Uygulama [ayarları](#app-settings) bölümünde uygulamanın yapılandırmasının gizli anahtarını aklınızda edin.

### <a name="app-settings"></a>Uygulama ayarları

Uygulama ayarları dosyasında ( `appsettings.json` veya `appsettings.Production.json` ), `ClientSecret` Azure Portal *sunucu* uygulamasının istemci gizli anahtarı ile bir giriş oluşturun:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Örneğin:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Kiracı yayımcısı etki alanı doğrulanmadıysa, Kullanıcı/istemci erişimi için sunucu API kapsamı, `https://` tabanlı BIR URI kullanır. Bu senaryoda, sunucu API uygulaması `Audience` dosyada yapılandırma gerektirir `appsettings.json` . Aşağıdaki yapılandırmada, `Audience` değerin sonu varsayılan kapsamı içermez **not** `/{DEFAULT SCOPE}` , burada yer tutucu `{DEFAULT SCOPE}` varsayılan kapsamdır:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> Önceki örnek yapılandırmasında:
>
> * Kiracı etki alanı `contoso.onmicrosoft.com` .
> * Sunucu API 'SI uygulaması `ClientId` `41451fa7-82d9-4673-8fa5-69eff5a761fd` .
>
> > [!NOTE]
> > `Audience`Genellikle açıkça yapılandırma, uygulamanın, tabanlı BIR API kapsamına sahip doğrulanmış bir yayımcı etki alanı ile birlikte gerekli **değildir** `api://` .
>
> Daha fazla bilgi için bkz. <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.

::: moniker-end

### <a name="authorization-policies"></a>Yetkilendirme ilkeleri

*Server* [authorization policies](xref:security/authorization/policies) `Startup.ConfigureServices` `Startup.cs` Grup nesne kimlikleri ve [AAD yönetici rolü nesne KIMLIKLERI](#aad-administrator-role-object-ids)temelinde, sunucu uygulamasının () AAD güvenlik grupları ve AAD yönetici rolleri için yetkilendirme ilkeleri oluşturun.

Örneğin, bir Azure Faturalandırma Yöneticisi rol ilkesi aşağıdaki yapılandırmaya sahiptir:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Daha fazla bilgi için bkz. <xref:security/authorization/policies>.

### <a name="controller-access"></a>Denetleyici erişimi

*Sunucu* uygulamasının denetleyicilerinde ilke gerektir.

Aşağıdaki örnek, `BillingDataController` ' dan Azure Faturalandırma yöneticileri ' nden, `BillingAdmin` [Yetkilendirme İlkeleri](#authorization-policies) bölümünde yapılandırıldığı gibi, ilke adı ile birlikte faturalandırma verilerine erişimi kısıtlar:

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a>Paketler

Aşağıdaki paketler için *sunucu* uygulamasına paket başvuruları ekleyin:

* [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity . İstemcilerinin](https://www.nuget.org/packages/Microsoft.Identity.Client)

### <a name="services"></a>Hizmetler

*Sunucu* uygulamasının `Startup.ConfigureServices` yönteminde, `Startup` *sunucu* uygulamasının sınıfındaki kod için ek ad alanları gerekir. Aşağıdaki ad alanlarını öğesine ekleyin `Startup.cs` :

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

Yapılandırılırken <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* İsteğe bağlı olarak için işlemeyi içerir <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Örneğin, uygulama başarısız kimlik doğrulamasını günlüğe kaydedebilir.
* İçinde <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , kullanıcının gruplarını ve rollerini almak için bir Graph API çağrısı yapın.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> günlüğe kaydetme iletilerinde kişisel olarak tanımlanabilir bilgiler (PII) sağlar. Yalnızca test Kullanıcı hesaplarıyla hata ayıklama için PII 'yi etkinleştirin.

`Startup.ConfigureServices` içinde:

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

Yukarıdaki kodda, aşağıdaki belirteç hatalarını işleme isteğe bağlıdır:

* `MsalUiRequiredException`: Uygulama yeterli izinlere (kapsamlar) sahip değil.
  * Azure portal içindeki sunucu API 'SI uygulama kapsamlarının için bir **uygulama** izni olup olmadığını belirleme `Directory.Read.All` .
  * Kiracı yöneticisinin uygulamaya izin vermiş olduğunu onaylayın.
* `MsalServiceException` ( `AADSTS70011` ): Kapsamın olduğunu onaylayın `https://graph.microsoft.com/.default` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a>Paketler

Aşağıdaki paketler için *sunucu* uygulamasına paket başvuruları ekleyin:

* [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="service-configuration"></a>Hizmet yapılandırması

*Sunucu* uygulamasının `Startup.ConfigureServices` yönteminde, Graph API çağrısı yapmak ve `group` kullanıcının güvenlik grupları ve rolleri için kullanıcı talepleri oluşturmak için mantık ekleyin.

> [!NOTE]
> Bu bölümdeki örnek kod, Microsoft Platform v 1.0 'ı temel alan Active Directory Authentication Library (ADAL) kullanır Identity .

`Startup` *Sunucu* uygulamasının sınıfındaki kod için ek ad alanları gereklidir. Aşağıdaki deyimler kümesi, `using` Bu bölümde aşağıdaki kod için gereken ad alanlarını içerir:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Yapılandırılırken <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* İsteğe bağlı olarak için işlemeyi içerir <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Örneğin, uygulama başarısız kimlik doğrulamasını günlüğe kaydedebilir.
* İçinde <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , kullanıcının gruplarını ve rollerini almak için bir Graph API çağrısı yapın.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> günlüğe kaydetme iletilerinde kişisel olarak tanımlanabilir bilgiler (PII) sağlar. Yalnızca test Kullanıcı hesaplarıyla hata ayıklama için PII 'yi etkinleştirin.

`Startup.ConfigureServices` içinde:

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

Yukarıdaki örnekte:

* <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Erişim BELIRTECI adal belirteç önbelleğinde zaten depolandığından, sessiz belirteç alımı () önce denenir. Yeni bir belirteç istemek yerine önbellekten belirteç alınması daha hızlıdır.
* Erişim belirteci önbellekten <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> alınmadıysa (veya oluşturulursa), <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> Kullanıcı () adına belirteci almak için istemci kimlik bilgileri () ile bir Kullanıcı onaylama () yapılır <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> . Sonra, `Microsoft.Graph.GraphServiceClient` Graph API çağrısı yapmak için belirteci kullanmaya devam edebilir. Belirteç ADAL belirteç önbelleğine yerleştirilir. Aynı kullanıcı için gelecekte Graph API çağrılar için belirteç, ile sessizce önbellekten alınır <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

::: moniker-end

İçindeki kod <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> geçişli üyelikleri elde etmez. Doğrudan ve geçişli üyelikleri elde etmek üzere kodu değiştirmek için:

* Kod satırı için:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Yukarıdaki satırı ile değiştirin:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Kod satırı için:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Yukarıdaki satırı ile değiştirin:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

İçindeki kod, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> talepler oluştururken AAD güvenlik grupları ve AAD yönetici rolleri arasında ayrım yapmaz. Uygulamanın gruplar ve roller arasında ayrım yapmak için `entry.ODataType` gruplar ve roller arasında ne zaman yinelediğini kontrol edin. Ayrı güvenlik grubu ve rol talepleri oluşturmak için aşağıdakilere benzer bir kod kullanın:

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>Kullanıcı tanımlı roller

AAD ile kaydedilen bir uygulama, Kullanıcı tanımlı rolleri kullanmak için de yapılandırılabilir.

Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `roles` , bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirteçte alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .

Aşağıdaki örnek, bir uygulamanın iki rolle yapılandırıldığını varsayar:

* `admin`
* `developer`

> [!NOTE]
> Azure AD Premium hesabı olmadan güvenlik gruplarına roller atayamasanız da, kullanıcıları rollere atayabilir ve `roles` Standart bir Azure hesabı olan kullanıcılar için bir talep alabilirsiniz. Bu bölümdeki kılavuz bir Azure AD Premium hesabı gerektirmez.
>
> Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.

`roles`AAD tarafından gönderilen tek talep, Kullanıcı tanımlı rolleri `appRoles` `value` bir JSON dizisinde öğeleri olarak sunar. Uygulama, rollerin JSON dizisini bağımsız taleplerine dönüştürmelidir `role` .

`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır. `CustomUserFactory` *`Client`* Blazor [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasına veya uygulamasına ekleyin ve kaydedin. Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .

`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya uygulamasında *`Client`* Blazor , `role` rol talebi olarak "" adlı talebi belirtin:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir. Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:

* [ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )
* [Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )

  Birden çok rol testi desteklenir:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a>AAD yönetici rolü nesne kimlikleri

Aşağıdaki tabloda sunulan nesne kimlikleri, talepler için [ilkeler](xref:security/authorization/policies) oluşturmak üzere kullanılır `group` . İlkeler, bir uygulamanın bir uygulamadaki çeşitli etkinlikler için kullanıcıları yetkilendirmesine izin verir. Daha fazla bilgi için [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümüne bakın.

AAD yönetici rolü | Nesne Kimliği
--- | ---
Uygulama Yöneticisi | fa11557b-4f15-4ddd-85d5-313c7cd74047
Uygulama geliştiricisi | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Kimlik doğrulama Yöneticisi | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps Yöneticisi | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection Yöneticisi | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C ıEF anahtar kümesi Yöneticisi | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C ıEF Ilke Yöneticisi | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C Kullanıcı akış Yöneticisi | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C Kullanıcı akışı öznitelik Yöneticisi | dd0baca0-a535-48c1-b871-8431abe16452
Faturalama yöneticisi | 69ff516a-b57d-4697-a429-9de4af7b5609
Bulut uygulaması Yöneticisi | 250b5fe3-b553-458d-9A53-b782c13c34bf
Bulut Cihaz Yöneticisi | 26cd4b44-2636-4DDB-BDFA-27feae66f86d
Uyumluluk Yöneticisi | 9d6e1dd0-c9f8-45F8-b558-b134f700116c
Uyumluluk verileri Yöneticisi | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Koşullu Erişim Yöneticisi | 8f71a611-137d-49af-87ad-e97f1fd5da76
Müşteri Kasası erişim onaylayıcısı | c18d54a8-b13e-4954-A1A4-7deaf2e4f184
Masaüstü Analizi Yöneticisi | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Dizin okuyucuları | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 Yöneticisi | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange yöneticisi | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Dış Identity sağlayıcı Yöneticisi | febfaeb4-e478-407a-b4b3-f4d9716618a2
Genel yönetici | a45ba61b-44db-462c-924b-3b2719152588
Genel okuyucu | f6903b21-6aba-4124-b44c-76671796b9d5
Grup Yöneticisi | 158b3e5a-d89d-460B-92B5-3b34985f0197
Konuk davetci | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Yardım Masası Yöneticisi | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune yöneticisi | 79950741-23fa-4189-b2cb-46640601c497
Kaizala Yöneticisi | d6322af2-48e7-42e0-8c68-0bbe31af3412
Lisans yöneticisi | 3355458a-E423-44bf-8b98-4ac5e572begin' 5
İleti Merkezi Gizlilik okuyucusu | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
İleti Merkezi okuyucusu | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office uygulamaları Yöneticisi | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Parola yöneticisi | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI Yöneticisi | 984e83b8-8337-4255-91a1-acb663175ab4
Power platformu Yöneticisi | 76d6f95e-9a15-4D7D-8d21-00de00faf9fd
Ayrıcalıklı kimlik doğrulama Yöneticisi | 0829f731-b46d-419F-9742-aeb122367d11
Ayrıcalıklı rol yöneticisi | f20a725a-d1c8-4107-83ea-1171c97d00c7
Rapor okuyucu | 54635450-e8ed-4f2d-9632-07db2517b4de
Yönetici ara | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Arama Düzenleyicisi | 6a6858c6-5f0d-44AC-87c7-0190fbedd271
Güvenlik yöneticisi | 20fa50e3-6531-44d8-bd39-b251420568ad
Güvenlik operatörü | 43aae017-8e51-4188-91ab-e6debd572800
Güvenlik okuyucusu | 45035cd3-fd97-4250-8197-3a53d3562d9b
Hizmet desteği yöneticisi | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint yöneticisi | e1c32229-875e-461d-ae24-3cb99116e86c
Skype Kurumsal yöneticisi | 0a8cee12-e21d-43EF-abd9-f1ea85710e30
Takımlar Iletişim Yöneticisi | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Takımlar Iletişimleri Destek Mühendisi | 802dd94e-d717-46F6-AF98-b9167071e9fc
Takımlar Iletişim uzmanı | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Takımlar Hizmet Yöneticisi | 8846a0be-197b-443A-b13c-11192691fa24
Kullanıcı yöneticisi | 1f6eed58-7dd3-460B-a298-666f975427a1

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
