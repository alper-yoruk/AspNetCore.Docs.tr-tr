---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 68071be9fb9f7a097c0c3693293bf8295e0173f1
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818813"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="c48de-103">Azure AD grupları, yönetim rolleri ve Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="c48de-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="c48de-104">Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c48de-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="c48de-105">Azure Active Directory (AAD), ASP.NET Core birleştirilebilecek çeşitli yetkilendirme yaklaşımları sağlar Identity :</span><span class="sxs-lookup"><span data-stu-id="c48de-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c48de-106">Kullanıcı tanımlı gruplar</span><span class="sxs-lookup"><span data-stu-id="c48de-106">User-defined groups</span></span>
  * <span data-ttu-id="c48de-107">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="c48de-107">Security</span></span>
  * <span data-ttu-id="c48de-108">O365</span><span class="sxs-lookup"><span data-stu-id="c48de-108">O365</span></span>
  * <span data-ttu-id="c48de-109">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="c48de-109">Distribution</span></span>
* <span data-ttu-id="c48de-110">Roller</span><span class="sxs-lookup"><span data-stu-id="c48de-110">Roles</span></span>
  * <span data-ttu-id="c48de-111">Yerleşik yönetim rolleri</span><span class="sxs-lookup"><span data-stu-id="c48de-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="c48de-112">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="c48de-112">User-defined roles</span></span>

<span data-ttu-id="c48de-113">Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="c48de-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="c48de-114">Microsoft Hesapları ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="c48de-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="c48de-115">AAD ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="c48de-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="c48de-116">AAD ile barındırılan</span><span class="sxs-lookup"><span data-stu-id="c48de-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="c48de-117">Microsoft Graph API izni</span><span class="sxs-lookup"><span data-stu-id="c48de-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="c48de-118">Beş taneden fazla yerleşik AAD yönetici rolü ve güvenlik grubu üyeliğine sahip tüm uygulama kullanıcıları için [MICROSOFT Graph API](/graph/use-the-api) çağrısı gerekir.</span><span class="sxs-lookup"><span data-stu-id="c48de-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="c48de-119">Graph API çağrılarına izin vermek için, Azure portal bir barındırılan çözümün tek başına veya istemci uygulamasına Blazor aşağıdaki [Graph API izinlerinden](/graph/permissions-reference) birini verin:</span><span class="sxs-lookup"><span data-stu-id="c48de-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="c48de-120">`Directory.Read.All`en az ayrıcalıklı izindir ve bu makalede açıklanan örnek için kullanılan izindir.</span><span class="sxs-lookup"><span data-stu-id="c48de-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="c48de-121">Kullanıcı tanımlı gruplar ve yerleşik yönetim rolleri</span><span class="sxs-lookup"><span data-stu-id="c48de-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="c48de-122">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c48de-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="c48de-123">Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve yerleşik yönetici rollerine atayın.</span><span class="sxs-lookup"><span data-stu-id="c48de-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="c48de-124">Azure AD güvenlik gruplarını kullanan roller</span><span class="sxs-lookup"><span data-stu-id="c48de-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="c48de-125">`groupMembershipClaims`özniteliğe</span><span class="sxs-lookup"><span data-stu-id="c48de-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="c48de-126">Aşağıdaki örneklerde, bir kullanıcının AAD yerleşik *Faturalama yöneticisi* rolüne atandığını varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c48de-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="c48de-127">`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="c48de-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="c48de-128">Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c48de-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="c48de-129">Atanan yerleşik Azure yönetim rolleri ve Kullanıcı tanımlı grupların sayısı beş aştığında AAD, `hasgroups` `true` talep göndermek yerine değeri olan bir talep gönderir `groups` .</span><span class="sxs-lookup"><span data-stu-id="c48de-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="c48de-130">En fazla beş rol ve gruba atanmış olan herhangi bir uygulama, bir kullanıcının rollerini ve gruplarını almak için ayrı bir Graph API çağrısı yapmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c48de-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="c48de-131">Bu makalede belirtilen örnek uygulama, bu senaryoyu ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c48de-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="c48de-132">Daha fazla bilgi için bkz `groups` `hasgroups` . [Microsoft Identity platform erişim belirteçleri](/azure/active-directory/develop/access-tokens#payload-claims) 'nde ve talep bilgileri: yük talepleri makalesi.</span><span class="sxs-lookup"><span data-stu-id="c48de-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="c48de-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Gruplar ve roller için dizi özelliklerini içerecek şekilde genişletin.</span><span class="sxs-lookup"><span data-stu-id="c48de-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="c48de-134">`null`Bu özellikler `foreach` daha sonra Döngülerde kullanıldığında gerekli olmaması için her özelliğe boş bir dizi atayın.</span><span class="sxs-lookup"><span data-stu-id="c48de-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="c48de-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="c48de-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="c48de-136">Bir barındırılan çözümün tek başına uygulamasında veya istemci uygulamasında Blazor özel bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c48de-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="c48de-137">Rol ve grup bilgilerini elde eden Graph API çağrıları için doğru kapsam (izin) kullanın.</span><span class="sxs-lookup"><span data-stu-id="c48de-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="c48de-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="c48de-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="c48de-139">`Program.Main`( `Program.cs` ) İçinde, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> uygulama hizmetini ekleyin ve <xref:System.Net.Http.HttpClient> Graph API istekleri yapmak için adlandırılmış bir ad ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c48de-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="c48de-140">Aşağıdaki örnekte istemcinin adı verilmiştir `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="c48de-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="c48de-141">Bir Graph API çağrısından açık veri Protokolü (OData) rollerini ve gruplarını almak için AAD dizin nesneleri sınıfları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c48de-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="c48de-142">OData JSON biçimine ulaştı ve <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> sınıfının bir örneğini dolduran bir çağrı `DirectoryObjects` .</span><span class="sxs-lookup"><span data-stu-id="c48de-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="c48de-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="c48de-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="c48de-144">Rolleri ve grup taleplerini işlemek için özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c48de-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="c48de-145">Aşağıdaki örnek uygulama, `roles` [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınan talep dizisini de işler.</span><span class="sxs-lookup"><span data-stu-id="c48de-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="c48de-146">Talep varsa `hasgroups` , <xref:System.Net.Http.HttpClient> kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak için adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c48de-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="c48de-147">Bu uygulama, Microsoft Identity Platform v 1.0 uç noktasını `https://graph.microsoft.com/v1.0/me/memberOf` ([API belgeleri](/graph/api/user-list-memberof)) kullanır.</span><span class="sxs-lookup"><span data-stu-id="c48de-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="c48de-148">Bu konudaki kılavuz, Identity msal paketleri v 2.0 için yükseltildiğinde v 2.0 için de güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="c48de-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="c48de-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="c48de-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="c48de-150">Varsa, özgün talebi kaldırmak için kod sağlamanız gerekmez, çünkü varsa, `groups` Framework tarafından otomatik olarak kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c48de-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="c48de-151">Bu örnekteki yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="c48de-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="c48de-152"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Giden isteklere erişim belirteçleri eklemek için özel bir sınıf ekler.</span><span class="sxs-lookup"><span data-stu-id="c48de-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="c48de-153"><xref:System.Net.Http.HttpClient>Güvenli, dış Web API uç noktası için Web API istekleri yapmak üzere bir adlandırılmış adı ekler.</span><span class="sxs-lookup"><span data-stu-id="c48de-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="c48de-154">, <xref:System.Net.Http.HttpClient> Yetkili istekleri yapmak için adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c48de-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="c48de-155">Bu yaklaşım için genel kapsam <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> makalesinde bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c48de-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="c48de-156">`Program.Main` `Program.cs` Bir barındırılan çözümün tek başına uygulamasının veya istemci uygulamasının fabrikasını () kaydedin Blazor .</span><span class="sxs-lookup"><span data-stu-id="c48de-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="c48de-157">`Directory.Read.All`Uygulama için ek kapsam olarak izin kapsamına onay:</span><span class="sxs-lookup"><span data-stu-id="c48de-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
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

<span data-ttu-id="c48de-158">İçindeki her grup veya rol için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="c48de-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="c48de-159">Aşağıdaki örnek, AAD yerleşik *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c48de-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="c48de-160">AAD rolü nesne kimliklerinin tamamı listesi için bkz. [AAD yönetim rolü grubu kimlikleri](#aad-adminstrative-role-group-ids) bölümü.</span><span class="sxs-lookup"><span data-stu-id="c48de-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="c48de-161">Aşağıdaki örneklerde, uygulama kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="c48de-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="c48de-162">[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c48de-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

<span data-ttu-id="c48de-163">Bir bileşenin tamamına erişim, [ `[Authorize]` öznitelik yönergesini](xref:blazor/security/index#authorize-attribute) () kullanarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :</span><span class="sxs-lookup"><span data-stu-id="c48de-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="c48de-164">Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="c48de-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="c48de-165">Bir ilke denetimi, [yordamsal mantığa kodunda de gerçekleştirilebilir](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="c48de-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="c48de-166">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="c48de-166">User-defined roles</span></span>

<span data-ttu-id="c48de-167">AAD ile kaydedilen bir uygulama, Kullanıcı tanımlı rolleri kullanmak için de yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c48de-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="c48de-168">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `roles` , bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirteçte alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .</span><span class="sxs-lookup"><span data-stu-id="c48de-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="c48de-169">Aşağıdaki örnek, bir uygulamanın iki rolle yapılandırıldığını varsayar:</span><span class="sxs-lookup"><span data-stu-id="c48de-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="c48de-170">Azure AD Premium hesabı olmadan güvenlik gruplarına roller atayamasanız da, kullanıcıları rollere atayabilir ve `roles` Standart bir Azure hesabı olan kullanıcılar için bir talep alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c48de-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="c48de-171">Bu bölümdeki kılavuz bir Azure AD Premium hesabı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="c48de-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="c48de-172">Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.</span><span class="sxs-lookup"><span data-stu-id="c48de-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="c48de-173">`roles`AAD tarafından gönderilen tek talep, Kullanıcı tanımlı rolleri `appRoles` `value` bir JSON dizisinde öğeleri olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="c48de-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="c48de-174">Uygulama, rollerin JSON dizisini bağımsız taleplerine dönüştürmelidir `role` .</span><span class="sxs-lookup"><span data-stu-id="c48de-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="c48de-175">`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c48de-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="c48de-176">`CustomUserFactory` Blazor [Kullanıcı TANıMLı gruplar ve AAD yerleşik yönetici rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasını veya istemci uygulamasını ekleyin ve kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c48de-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="c48de-177">Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .</span><span class="sxs-lookup"><span data-stu-id="c48de-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="c48de-178">`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya istemci uygulamasında Blazor , `role` rol talebi olarak "" adlı talebi belirtin:</span><span class="sxs-lookup"><span data-stu-id="c48de-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="c48de-179">Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="c48de-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="c48de-180">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="c48de-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="c48de-181">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="c48de-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="c48de-182">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="c48de-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="c48de-183">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="c48de-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="c48de-184">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="c48de-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="c48de-185">AAD yönetim rolü grup kimlikleri</span><span class="sxs-lookup"><span data-stu-id="c48de-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="c48de-186">Aşağıdaki tabloda sunulan nesne kimlikleri, talepler için [ilkeler](xref:security/authorization/policies) oluşturmak üzere kullanılır `group` .</span><span class="sxs-lookup"><span data-stu-id="c48de-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="c48de-187">İlkeler, bir uygulamanın bir uygulamadaki çeşitli etkinlikler için kullanıcıları yetkilendirmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="c48de-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="c48de-188">Daha fazla bilgi için [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c48de-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="c48de-189">AAD yönetim rolü</span><span class="sxs-lookup"><span data-stu-id="c48de-189">AAD Administrative Role</span></span> | <span data-ttu-id="c48de-190">Nesne Kimliği</span><span class="sxs-lookup"><span data-stu-id="c48de-190">Object ID</span></span>
--- | ---
<span data-ttu-id="c48de-191">Uygulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-191">Application administrator</span></span> | <span data-ttu-id="c48de-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="c48de-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="c48de-193">Uygulama geliştiricisi</span><span class="sxs-lookup"><span data-stu-id="c48de-193">Application developer</span></span> | <span data-ttu-id="c48de-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="c48de-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="c48de-195">Kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-195">Authentication administrator</span></span> | <span data-ttu-id="c48de-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="c48de-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="c48de-197">Azure DevOps Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-197">Azure DevOps administrator</span></span> | <span data-ttu-id="c48de-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="c48de-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="c48de-199">Azure Information Protection Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="c48de-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="c48de-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="c48de-201">B2C ıEF anahtar kümesi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="c48de-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="c48de-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="c48de-203">B2C ıEF Ilke Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="c48de-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="c48de-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="c48de-205">B2C Kullanıcı akış Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-205">B2C user flow administrator</span></span> | <span data-ttu-id="c48de-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="c48de-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="c48de-207">B2C Kullanıcı akışı öznitelik Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="c48de-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="c48de-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="c48de-209">Faturalama yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-209">Billing administrator</span></span> | <span data-ttu-id="c48de-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="c48de-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="c48de-211">Bulut uygulaması Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-211">Cloud application administrator</span></span> | <span data-ttu-id="c48de-212">250b5fe3-b553-458d-9A53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="c48de-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="c48de-213">Bulut Cihaz Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-213">Cloud device administrator</span></span> | <span data-ttu-id="c48de-214">26cd4b44-2636-4DDB-BDFA-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="c48de-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="c48de-215">Uyumluluk Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-215">Compliance administrator</span></span> | <span data-ttu-id="c48de-216">9d6e1dd0-c9f8-45F8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="c48de-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="c48de-217">Uyumluluk verileri Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-217">Compliance data administrator</span></span> | <span data-ttu-id="c48de-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="c48de-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="c48de-219">Koşullu Erişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-219">Conditional Access administrator</span></span> | <span data-ttu-id="c48de-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="c48de-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="c48de-221">Müşteri Kasası erişim onaylayıcısı</span><span class="sxs-lookup"><span data-stu-id="c48de-221">Customer LockBox access approver</span></span> | <span data-ttu-id="c48de-222">c18d54a8-b13e-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="c48de-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="c48de-223">Masaüstü Analizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="c48de-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="c48de-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="c48de-225">Dizin okuyucuları</span><span class="sxs-lookup"><span data-stu-id="c48de-225">Directory readers</span></span> | <span data-ttu-id="c48de-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="c48de-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="c48de-227">Dynamics 365 Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="c48de-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="c48de-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="c48de-229">Exchange yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-229">Exchange administrator</span></span> | <span data-ttu-id="c48de-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="c48de-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="c48de-231">Dış Identity sağlayıcı Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-231">External Identity Provider administrator</span></span> | <span data-ttu-id="c48de-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="c48de-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="c48de-233">Genel yönetici</span><span class="sxs-lookup"><span data-stu-id="c48de-233">Global administrator</span></span> | <span data-ttu-id="c48de-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="c48de-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="c48de-235">Genel okuyucu</span><span class="sxs-lookup"><span data-stu-id="c48de-235">Global reader</span></span> | <span data-ttu-id="c48de-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="c48de-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="c48de-237">Grup Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-237">Groups administrator</span></span> | <span data-ttu-id="c48de-238">158b3e5a-d89d-460B-92B5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="c48de-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="c48de-239">Konuk davetci</span><span class="sxs-lookup"><span data-stu-id="c48de-239">Guest inviter</span></span> | <span data-ttu-id="c48de-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="c48de-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="c48de-241">Yardım Masası Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-241">Helpdesk administrator</span></span> | <span data-ttu-id="c48de-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="c48de-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="c48de-243">Intune yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-243">Intune administrator</span></span> | <span data-ttu-id="c48de-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="c48de-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="c48de-245">Kaizala Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-245">Kaizala administrator</span></span> | <span data-ttu-id="c48de-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="c48de-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="c48de-247">Lisans yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-247">License administrator</span></span> | <span data-ttu-id="c48de-248">3355458a-E423-44bf-8b98-4ac5e572begin' 5</span><span class="sxs-lookup"><span data-stu-id="c48de-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="c48de-249">İleti Merkezi Gizlilik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="c48de-249">Message center privacy reader</span></span> | <span data-ttu-id="c48de-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="c48de-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="c48de-251">İleti Merkezi okuyucusu</span><span class="sxs-lookup"><span data-stu-id="c48de-251">Message center reader</span></span> | <span data-ttu-id="c48de-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="c48de-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="c48de-253">Office uygulamaları Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-253">Office apps administrator</span></span> | <span data-ttu-id="c48de-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="c48de-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="c48de-255">Parola yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-255">Password administrator</span></span> | <span data-ttu-id="c48de-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="c48de-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="c48de-257">Power BI Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-257">Power BI administrator</span></span> | <span data-ttu-id="c48de-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="c48de-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="c48de-259">Power platformu Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-259">Power platform administrator</span></span> | <span data-ttu-id="c48de-260">76d6f95e-9a15-4D7D-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="c48de-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="c48de-261">Ayrıcalıklı kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-261">Privileged authentication administrator</span></span> | <span data-ttu-id="c48de-262">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="c48de-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="c48de-263">Ayrıcalıklı rol yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-263">Privileged role administrator</span></span> | <span data-ttu-id="c48de-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="c48de-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="c48de-265">Rapor okuyucu</span><span class="sxs-lookup"><span data-stu-id="c48de-265">Reports reader</span></span> | <span data-ttu-id="c48de-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="c48de-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="c48de-267">Yönetici ara</span><span class="sxs-lookup"><span data-stu-id="c48de-267">Search administrator</span></span> | <span data-ttu-id="c48de-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="c48de-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="c48de-269">Arama Düzenleyicisi</span><span class="sxs-lookup"><span data-stu-id="c48de-269">Search editor</span></span> | <span data-ttu-id="c48de-270">6a6858c6-5f0d-44AC-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="c48de-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="c48de-271">Güvenlik yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-271">Security administrator</span></span> | <span data-ttu-id="c48de-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="c48de-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="c48de-273">Güvenlik operatörü</span><span class="sxs-lookup"><span data-stu-id="c48de-273">Security operator</span></span> | <span data-ttu-id="c48de-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="c48de-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="c48de-275">Güvenlik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="c48de-275">Security reader</span></span> | <span data-ttu-id="c48de-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="c48de-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="c48de-277">Hizmet desteği yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-277">Service support administrator</span></span> | <span data-ttu-id="c48de-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="c48de-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="c48de-279">SharePoint yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-279">SharePoint administrator</span></span> | <span data-ttu-id="c48de-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="c48de-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="c48de-281">Skype Kurumsal yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-281">Skype for Business administrator</span></span> | <span data-ttu-id="c48de-282">0a8cee12-e21d-43EF-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="c48de-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="c48de-283">Takımlar Iletişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-283">Teams Communications Administrator</span></span> | <span data-ttu-id="c48de-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="c48de-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="c48de-285">Takımlar Iletişimleri Destek Mühendisi</span><span class="sxs-lookup"><span data-stu-id="c48de-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="c48de-286">802dd94e-d717-46F6-AF98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="c48de-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="c48de-287">Takımlar Iletişim uzmanı</span><span class="sxs-lookup"><span data-stu-id="c48de-287">Teams Communications Specialist</span></span> | <span data-ttu-id="c48de-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="c48de-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="c48de-289">Takımlar Hizmet Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-289">Teams Service Administrator</span></span> | <span data-ttu-id="c48de-290">8846a0be-197b-443A-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="c48de-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="c48de-291">Kullanıcı yöneticisi</span><span class="sxs-lookup"><span data-stu-id="c48de-291">User administrator</span></span> | <span data-ttu-id="c48de-292">1f6eed58-7dd3-460B-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="c48de-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c48de-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c48de-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
