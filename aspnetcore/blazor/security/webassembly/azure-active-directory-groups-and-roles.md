---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058265"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="20b09-103">Azure Active Directory (AAD) grupları, yönetici rolleri ve Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="20b09-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="20b09-104">Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="20b09-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="20b09-105">Bu makale, Blazor Microsoft 1,0 ile ASP.NET Core Apps sürüm 3,1 için geçerlidir Identity ve Identity kısa bir süre içinde 2,0 ile 5,0 güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="20b09-106">Daha fazla bilgi için, aşağıdaki GitHub sorununa ve çekme isteğine bakın.</span><span class="sxs-lookup"><span data-stu-id="20b09-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="20b09-107">Çekme isteğinin **dosyalar değişti** sekmesi, taslak metnini ve bu makaledeki güncelleştirmelere yönelik örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="20b09-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="20b09-108">İnceleme ve son güncelleştirmeler sonrasında, çekme isteği canlı belge kümesiyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="20b09-109">Sorun: [ Blazor AAD grupları ve rolleri ile ilgili (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="20b09-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="20b09-110">Çekme Isteği: [ Blazor AAD grupları ve rolleri konu 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="20b09-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="20b09-111">Azure Active Directory (AAD), ile birleştirilebilir çeşitli yetkilendirme yaklaşımları sağlar ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="20b09-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="20b09-112">Kullanıcı tanımlı gruplar</span><span class="sxs-lookup"><span data-stu-id="20b09-112">User-defined groups</span></span>
  * <span data-ttu-id="20b09-113">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="20b09-113">Security</span></span>
  * <span data-ttu-id="20b09-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="20b09-114">Microsoft 365</span></span>
  * <span data-ttu-id="20b09-115">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="20b09-115">Distribution</span></span>
* <span data-ttu-id="20b09-116">Roller</span><span class="sxs-lookup"><span data-stu-id="20b09-116">Roles</span></span>
  * <span data-ttu-id="20b09-117">AAD yönetici rolleri</span><span class="sxs-lookup"><span data-stu-id="20b09-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="20b09-118">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="20b09-118">User-defined roles</span></span>

<span data-ttu-id="20b09-119">Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="20b09-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="20b09-120">Microsoft Hesapları ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="20b09-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="20b09-121">AAD ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="20b09-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="20b09-122">AAD ile barındırılan</span><span class="sxs-lookup"><span data-stu-id="20b09-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="20b09-123">Kapsamlar</span><span class="sxs-lookup"><span data-stu-id="20b09-123">Scopes</span></span>

<span data-ttu-id="20b09-124">5 ' ten fazla AAD yönetici rolü ve güvenlik grubu üyeliğine sahip tüm uygulama kullanıcıları için [MICROSOFT Graph API](/graph/use-the-api) çağrısı gerekir.</span><span class="sxs-lookup"><span data-stu-id="20b09-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="20b09-125">Graph API çağrılarına izin vermek için, *`Client`* Azure Portal bir barındırılan çözümün tek başına veya uygulamasına Blazor Şu [Graph API izinlerinden (kapsamlar)](/graph/permissions-reference) sahip olduğunu verin:</span><span class="sxs-lookup"><span data-stu-id="20b09-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="20b09-126">`Directory.Read.All` en az ayrıcalıklı kapsamdır ve bu makalede açıklanan örnek için kullanılan kapsamdır.</span><span class="sxs-lookup"><span data-stu-id="20b09-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="20b09-127">Kullanıcı tanımlı gruplar ve yönetici rolleri</span><span class="sxs-lookup"><span data-stu-id="20b09-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="20b09-128">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="20b09-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="20b09-129">Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve AAD yönetici rollerine atayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="20b09-130">Azure AD güvenlik gruplarını kullanan roller</span><span class="sxs-lookup"><span data-stu-id="20b09-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="20b09-131">`groupMembershipClaims` özniteliğe</span><span class="sxs-lookup"><span data-stu-id="20b09-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="20b09-132">Aşağıdaki örneklerde, bir kullanıcının AAD *faturalandırma Yöneticisi* rolüne atandığını varsayılır.</span><span class="sxs-lookup"><span data-stu-id="20b09-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="20b09-133">`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="20b09-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="20b09-134">Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="20b09-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="20b09-135">Atanan AAD Yönetici rollerinin ve Kullanıcı tanımlı grupların sayısı beş aştığında AAD, `hasgroups` `true` talep göndermek yerine değeri olan bir talep gönderir `groups` .</span><span class="sxs-lookup"><span data-stu-id="20b09-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="20b09-136">En fazla beş rol ve gruba atanmış olan herhangi bir uygulama, bir kullanıcının rollerini ve gruplarını almak için ayrı bir Graph API çağrısı yapmalıdır.</span><span class="sxs-lookup"><span data-stu-id="20b09-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="20b09-137">Bu makalede belirtilen örnek uygulama, bu senaryoyu ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="20b09-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="20b09-138">Daha fazla bilgi için bkz `groups` `hasgroups` . [Microsoft Identity platform erişim belirteçleri](/azure/active-directory/develop/access-tokens#payload-claims) 'nde ve talep bilgileri: yük talepleri makalesi.</span><span class="sxs-lookup"><span data-stu-id="20b09-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="20b09-139"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Gruplar ve roller için dizi özelliklerini içerecek şekilde genişletin.</span><span class="sxs-lookup"><span data-stu-id="20b09-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="20b09-140">`null`Bu özellikler `foreach` daha sonra Döngülerde kullanıldığında gerekli olmaması için her özelliğe boş bir dizi atayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="20b09-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="20b09-141">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="20b09-142">AAD grupları ve rolleri için talepler oluşturmak için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="20b09-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="20b09-143">Graph SDK 'sını kullanma</span><span class="sxs-lookup"><span data-stu-id="20b09-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="20b09-144">Adlandırılmış bir ad kullanın `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="20b09-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="20b09-145">Graph SDK 'sını kullanma</span><span class="sxs-lookup"><span data-stu-id="20b09-145">Use the Graph SDK</span></span>

<span data-ttu-id="20b09-146">İçin bir barındırılan çözümün tek başına uygulamasına veya uygulamasına bir paket başvurusu ekleyin *`Client`* Blazor [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="20b09-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="20b09-147">Graph SDK yardımcı program sınıflarını ve yapılandırmasını makalenin *Graph SDK* bölümüne ekleyin <xref:blazor/security/webassembly/graph-api#graph-sdk> .</span><span class="sxs-lookup"><span data-stu-id="20b09-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="20b09-148">Aşağıdaki özel kullanıcı hesabı fabrikasını bir barındırılan çözümün tek başına appo veya *`Client`* uygulamasına ekleyin Blazor ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="20b09-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="20b09-149">Özel Kullanıcı fabrikası, rol ve grup taleplerini işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20b09-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="20b09-150">`roles`Talep dizisi, [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="20b09-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="20b09-151">Talep varsa `hasgroups` , kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak üzere Graf SDK 'sı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="20b09-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

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

<span data-ttu-id="20b09-152">Yukarıdaki kod geçişli üyelikleri içermez.</span><span class="sxs-lookup"><span data-stu-id="20b09-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="20b09-153">Uygulama, doğrudan ve geçişli grup üyeliği talepleri gerektiriyorsa:</span><span class="sxs-lookup"><span data-stu-id="20b09-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="20b09-154">`IUserMemberOfCollectionWithReferencesPage`Türünü `groupsAndAzureRoles` olarak değiştirin `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="20b09-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="20b09-155">Kullanıcının grupları ve rolleri istendiğinde, `MemberOf` özelliğini ile değiştirin `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="20b09-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="20b09-156">`Program.Main`( `Program.cs` ) IÇINDE, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın: uygulama, genişleten özel kullanıcı hesabı sınıfını kullanıyorsa <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , aşağıdaki kodda için özel kullanıcı hesabı sınıfını değiştirin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> :</span><span class="sxs-lookup"><span data-stu-id="20b09-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="20b09-157">Adlandırılmış bir ad kullanın `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="20b09-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="20b09-158">Tek başına uygulamada veya *`Client`* barındırılan bir Blazor çözümün uygulamasında özel bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20b09-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="20b09-159">Rol ve grup bilgilerini elde eden Graph API çağrıları için doğru kapsamı kullanın.</span><span class="sxs-lookup"><span data-stu-id="20b09-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="20b09-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="20b09-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="20b09-161">`Program.Main`( `Program.cs` ) İçinde, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> uygulama hizmetini ekleyin ve <xref:System.Net.Http.HttpClient> Graph API istekleri yapmak için adlandırılmış bir ad ekleyin.</span><span class="sxs-lookup"><span data-stu-id="20b09-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="20b09-162">Aşağıdaki örnekte istemcinin adı verilmiştir `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="20b09-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="20b09-163">Bir Graph API çağrısından açık veri Protokolü (OData) rollerini ve gruplarını almak için AAD dizin nesneleri sınıfları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20b09-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="20b09-164">OData JSON biçimine ulaştı ve <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> sınıfının bir örneğini dolduran bir çağrı `DirectoryObjects` .</span><span class="sxs-lookup"><span data-stu-id="20b09-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="20b09-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="20b09-165">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="20b09-166">Rolleri ve grup taleplerini işlemek için özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20b09-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="20b09-167">Aşağıdaki örnek uygulama, `roles` [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınan talep dizisini de işler.</span><span class="sxs-lookup"><span data-stu-id="20b09-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="20b09-168">Talep varsa `hasgroups` , <xref:System.Net.Http.HttpClient> kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak için adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20b09-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="20b09-169">Bu uygulama, Microsoft Identity Platform v 1.0 uç noktasını `https://graph.microsoft.com/v1.0/me/memberOf` ([API belgeleri](/graph/api/user-list-memberof)) kullanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="20b09-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="20b09-170">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="20b09-171">Varsa, özgün talebi kaldırmak için kod sağlamanız gerekmez, çünkü varsa, `groups` Framework tarafından otomatik olarak kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="20b09-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="20b09-172">Bu örnekteki yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="20b09-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="20b09-173"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Giden isteklere erişim belirteçleri eklemek için özel bir sınıf ekler.</span><span class="sxs-lookup"><span data-stu-id="20b09-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="20b09-174"><xref:System.Net.Http.HttpClient>Güvenli, dış Web API uç noktası için Web API istekleri yapmak üzere bir adlandırılmış adı ekler.</span><span class="sxs-lookup"><span data-stu-id="20b09-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="20b09-175">, <xref:System.Net.Http.HttpClient> Yetkili istekleri yapmak için adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="20b09-176">Bu yaklaşım için genel kapsam <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> makalesinde bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20b09-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="20b09-177">`Program.Main` `Program.cs` Bir barındırılan çözümün tek başına uygulamasının veya uygulamasının fabrikasını () kaydedin *`Client`* Blazor .</span><span class="sxs-lookup"><span data-stu-id="20b09-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="20b09-178">`Directory.Read.All`Uygulama için ek kapsam olarak kapsama onay:</span><span class="sxs-lookup"><span data-stu-id="20b09-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="20b09-179">Yetkilendirme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="20b09-179">Authorization configuration</span></span>

<span data-ttu-id="20b09-180">İçindeki her grup veya rol için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="20b09-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="20b09-181">Aşağıdaki örnek AAD *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="20b09-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="20b09-182">AAD rolü nesne kimliklerinin tamamı listesi için bkz. [AAD yönetici rolü nesne kimlikleri](#aad-administrator-role-object-ids) bölümü.</span><span class="sxs-lookup"><span data-stu-id="20b09-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="20b09-183">Aşağıdaki örneklerde, uygulama kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="20b09-184">[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="20b09-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="20b09-185">Bir bileşenin tamamına erişim, [ `[Authorize]` öznitelik yönergesini](xref:blazor/security/index#authorize-attribute) () kullanarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :</span><span class="sxs-lookup"><span data-stu-id="20b09-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="20b09-186">Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="20b09-187">Bir ilke denetimi, [yordamsal mantığa kodunda de gerçekleştirilebilir](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="20b09-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="20b09-188">Kullanıcı tanımlı gruplar ve yönetici rolleri için sunucu API 'SI erişimini yetkilendir</span><span class="sxs-lookup"><span data-stu-id="20b09-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="20b09-189">Sunucu API 'SI, istemci tarafı WebAssembly uygulamasındaki sayfalara ve kaynaklara erişmek için yetkilendirmeye ek olarak, kullanıcılara güvenli API uç noktalarına erişim yetkisi verebilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="20b09-190">*Sunucu* uygulaması kullanıcının erişim belirtecini doğruladıktan sonra:</span><span class="sxs-lookup"><span data-stu-id="20b09-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="20b09-191">Sunucu API 'SI uygulaması, Graph API için bir erişim belirteci almak için, kullanıcının kendi erişim belirtecinden sabit [nesne tanımlayıcı talebini ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) kullanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="20b09-192">Bir Graph API çağrısı kullanıcının Kullanıcı tarafından tanımlanan Azure güvenlik grubunu ve yönetici rolü üyeliklerini, kullanıcıyı çağırarak edinir [`memberOf`](/graph/api/user-list-memberof) .</span><span class="sxs-lookup"><span data-stu-id="20b09-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="20b09-193">Üyelikler, talepler oluşturmak için kullanılır `group` .</span><span class="sxs-lookup"><span data-stu-id="20b09-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="20b09-194">[Yetkilendirme İlkeleri](xref:security/authorization/policies) , uygulama genelınde sunucu API uç noktalarına Kullanıcı erişimini sınırlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="20b09-195">Bu kılavuz Şu anda kullanıcıları [AAD Kullanıcı tanımlı rollerinin](#user-defined-roles)temelinde yetkilendirmeye dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="20b09-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="20b09-196">Bu bölümdeki kılavuz, sunucu API uygulamasını Microsoft Graph API çağrısı için bir [*Daemon uygulaması*](/azure/active-directory/develop/scenario-daemon-overview) olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="20b09-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="20b09-197">Bu yaklaşım şu **değildir**:</span><span class="sxs-lookup"><span data-stu-id="20b09-197">This approach does **not**:</span></span>

* <span data-ttu-id="20b09-198">`access_as_user`Kapsam gerektir.</span><span class="sxs-lookup"><span data-stu-id="20b09-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="20b09-199">API isteğini yapan kullanıcı/istemci adına Graph API erişin.</span><span class="sxs-lookup"><span data-stu-id="20b09-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="20b09-200">Sunucu API 'SI uygulaması tarafından Graph API çağrısı yalnızca Azure portal için bir sunucu API uygulama **uygulaması** Graph API kapsamı gerektirir `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="20b09-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="20b09-201">Bu yaklaşım, istemci uygulamanın, sunucu API 'sinin açıkça izin vermediği dizin verilerine erişmesini kesinlikle önler.</span><span class="sxs-lookup"><span data-stu-id="20b09-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="20b09-202">İstemci uygulaması yalnızca sunucu API 'SI uygulamasının denetleyici uç noktalarına erişebilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="20b09-203">Azure yapılandırması</span><span class="sxs-lookup"><span data-stu-id="20b09-203">Azure configuration</span></span>

* <span data-ttu-id="20b09-204">*Sunucu* **uygulaması kaydına** ,  `Directory.Read.All` güvenlik grupları için en az ayrıcalıklı erişim düzeyi olan, için Graph API kapsam (temsilci yok) verildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="20b09-205">Kapsam atamasını yaptıktan sonra yönetim izninin kapsama uygulandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="20b09-206">*Sunucu* uygulamasına yeni bir istemci parolası atayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="20b09-207">Uygulama [ayarları](#app-settings) bölümünde uygulamanın yapılandırmasının gizli anahtarını aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="20b09-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="20b09-208">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="20b09-208">App settings</span></span>

<span data-ttu-id="20b09-209">Uygulama ayarları dosyasında ( `appsettings.json` veya `appsettings.Production.json` ), `ClientSecret` Azure Portal *sunucu* uygulamasının istemci gizli anahtarı ile bir giriş oluşturun:</span><span class="sxs-lookup"><span data-stu-id="20b09-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="20b09-210">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="20b09-210">For example:</span></span>

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
> <span data-ttu-id="20b09-211">Kiracı yayımcısı etki alanı doğrulanmadıysa, Kullanıcı/istemci erişimi için sunucu API kapsamı, `https://` tabanlı BIR URI kullanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="20b09-212">Bu senaryoda, sunucu API uygulaması `Audience` dosyada yapılandırma gerektirir `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="20b09-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="20b09-213">Aşağıdaki yapılandırmada, `Audience` değerin sonu varsayılan kapsamı içermez  `/{DEFAULT SCOPE}` , burada yer tutucu `{DEFAULT SCOPE}` varsayılan kapsamdır:</span><span class="sxs-lookup"><span data-stu-id="20b09-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
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
> <span data-ttu-id="20b09-214">Önceki örnek yapılandırmasında:</span><span class="sxs-lookup"><span data-stu-id="20b09-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="20b09-215">Kiracı etki alanı `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="20b09-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="20b09-216">Sunucu API 'SI uygulaması `ClientId` `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="20b09-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="20b09-217">`Audience`Genellikle açıkça yapılandırma, uygulamanın, tabanlı BIR API kapsamına sahip doğrulanmış bir yayımcı etki alanı ile birlikte gerekli **değildir** `api://` .</span><span class="sxs-lookup"><span data-stu-id="20b09-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="20b09-218">Daha fazla bilgi için bkz. <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="20b09-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="20b09-219">Yetkilendirme ilkeleri</span><span class="sxs-lookup"><span data-stu-id="20b09-219">Authorization policies</span></span>

<span data-ttu-id="20b09-220"> [](xref:security/authorization/policies) `Startup.ConfigureServices` `Startup.cs` Grup nesne kimlikleri ve [AAD yönetici rolü nesne KIMLIKLERI](#aad-administrator-role-object-ids)temelinde, sunucu uygulamasının () AAD güvenlik grupları ve AAD yönetici rolleri için yetkilendirme ilkeleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20b09-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="20b09-221">Örneğin, bir Azure Faturalandırma Yöneticisi rol ilkesi aşağıdaki yapılandırmaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="20b09-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="20b09-222">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="20b09-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="20b09-223">Denetleyici erişimi</span><span class="sxs-lookup"><span data-stu-id="20b09-223">Controller access</span></span>

<span data-ttu-id="20b09-224">*Sunucu* uygulamasının denetleyicilerinde ilke gerektir.</span><span class="sxs-lookup"><span data-stu-id="20b09-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="20b09-225">Aşağıdaki örnek, `BillingDataController` ' dan Azure Faturalandırma yöneticileri ' nden, `BillingAdmin` [Yetkilendirme İlkeleri](#authorization-policies) bölümünde yapılandırıldığı gibi, ilke adı ile birlikte faturalandırma verilerine erişimi kısıtlar:</span><span class="sxs-lookup"><span data-stu-id="20b09-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

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

### <a name="packages"></a><span data-ttu-id="20b09-226">Paketler</span><span class="sxs-lookup"><span data-stu-id="20b09-226">Packages</span></span>

<span data-ttu-id="20b09-227">Aşağıdaki paketler için *sunucu* uygulamasına paket başvuruları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="20b09-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="20b09-228">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="20b09-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="20b09-229">[Microsoft. Identity . İstemcilerinin](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="20b09-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="20b09-230">Hizmetler</span><span class="sxs-lookup"><span data-stu-id="20b09-230">Services</span></span>

<span data-ttu-id="20b09-231">*Sunucu* uygulamasının `Startup.ConfigureServices` yönteminde, `Startup` *sunucu* uygulamasının sınıfındaki kod için ek ad alanları gerekir.</span><span class="sxs-lookup"><span data-stu-id="20b09-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="20b09-232">Aşağıdaki ad alanlarını öğesine ekleyin `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="20b09-232">Add the following namespaces to `Startup.cs`:</span></span>

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

<span data-ttu-id="20b09-233">Yapılandırılırken <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="20b09-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="20b09-234">İsteğe bağlı olarak için işlemeyi içerir <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="20b09-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="20b09-235">Örneğin, uygulama başarısız kimlik doğrulamasını günlüğe kaydedebilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="20b09-236">İçinde <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , kullanıcının gruplarını ve rollerini almak için bir Graph API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="20b09-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="20b09-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> günlüğe kaydetme iletilerinde kişisel olarak tanımlanabilir bilgiler (PII) sağlar.</span><span class="sxs-lookup"><span data-stu-id="20b09-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="20b09-238">Yalnızca test Kullanıcı hesaplarıyla hata ayıklama için PII 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="20b09-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="20b09-239">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="20b09-239">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="20b09-240">Yukarıdaki kodda, aşağıdaki belirteç hatalarını işleme isteğe bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="20b09-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="20b09-241">`MsalUiRequiredException`: Uygulama yeterli izinlere (kapsamlar) sahip değil.</span><span class="sxs-lookup"><span data-stu-id="20b09-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="20b09-242">Azure portal içindeki sunucu API 'SI uygulama kapsamlarının için bir **uygulama** izni olup olmadığını belirleme `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="20b09-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="20b09-243">Kiracı yöneticisinin uygulamaya izin vermiş olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="20b09-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="20b09-244">`MsalServiceException` ( `AADSTS70011` ): Kapsamın olduğunu onaylayın `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="20b09-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="20b09-245">Paketler</span><span class="sxs-lookup"><span data-stu-id="20b09-245">Packages</span></span>

<span data-ttu-id="20b09-246">Aşağıdaki paketler için *sunucu* uygulamasına paket başvuruları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="20b09-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="20b09-247">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="20b09-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="20b09-248">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="20b09-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="20b09-249">Hizmet yapılandırması</span><span class="sxs-lookup"><span data-stu-id="20b09-249">Service configuration</span></span>

<span data-ttu-id="20b09-250">*Sunucu* uygulamasının `Startup.ConfigureServices` yönteminde, Graph API çağrısı yapmak ve `group` kullanıcının güvenlik grupları ve rolleri için kullanıcı talepleri oluşturmak için mantık ekleyin.</span><span class="sxs-lookup"><span data-stu-id="20b09-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="20b09-251">Bu bölümdeki örnek kod, Microsoft Platform v 1.0 'ı temel alan Active Directory Authentication Library (ADAL) kullanır Identity .</span><span class="sxs-lookup"><span data-stu-id="20b09-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="20b09-252">`Startup` *Sunucu* uygulamasının sınıfındaki kod için ek ad alanları gereklidir.</span><span class="sxs-lookup"><span data-stu-id="20b09-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="20b09-253">Aşağıdaki deyimler kümesi, `using` Bu bölümde aşağıdaki kod için gereken ad alanlarını içerir:</span><span class="sxs-lookup"><span data-stu-id="20b09-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="20b09-254">Yapılandırılırken <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="20b09-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="20b09-255">İsteğe bağlı olarak için işlemeyi içerir <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="20b09-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="20b09-256">Örneğin, uygulama başarısız kimlik doğrulamasını günlüğe kaydedebilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="20b09-257">İçinde <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , kullanıcının gruplarını ve rollerini almak için bir Graph API çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="20b09-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="20b09-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> günlüğe kaydetme iletilerinde kişisel olarak tanımlanabilir bilgiler (PII) sağlar.</span><span class="sxs-lookup"><span data-stu-id="20b09-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="20b09-259">Yalnızca test Kullanıcı hesaplarıyla hata ayıklama için PII 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="20b09-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="20b09-260">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="20b09-260">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="20b09-261">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="20b09-261">In the preceding example:</span></span>

* <span data-ttu-id="20b09-262"><xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Erişim BELIRTECI adal belirteç önbelleğinde zaten depolandığından, sessiz belirteç alımı () önce denenir.</span><span class="sxs-lookup"><span data-stu-id="20b09-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="20b09-263">Yeni bir belirteç istemek yerine önbellekten belirteç alınması daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="20b09-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="20b09-264">Erişim belirteci önbellekten <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> alınmadıysa (veya oluşturulursa), <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> Kullanıcı () adına belirteci almak için istemci kimlik bilgileri () ile bir Kullanıcı onaylama () yapılır <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="20b09-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="20b09-265">Sonra, `Microsoft.Graph.GraphServiceClient` Graph API çağrısı yapmak için belirteci kullanmaya devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="20b09-266">Belirteç ADAL belirteç önbelleğine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="20b09-267">Aynı kullanıcı için gelecekte Graph API çağrılar için belirteç, ile sessizce önbellekten alınır <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="20b09-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="20b09-268">İçindeki kod <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> geçişli üyelikleri elde etmez.</span><span class="sxs-lookup"><span data-stu-id="20b09-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="20b09-269">Doğrudan ve geçişli üyelikleri elde etmek üzere kodu değiştirmek için:</span><span class="sxs-lookup"><span data-stu-id="20b09-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="20b09-270">Kod satırı için:</span><span class="sxs-lookup"><span data-stu-id="20b09-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="20b09-271">Yukarıdaki satırı ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="20b09-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="20b09-272">Kod satırı için:</span><span class="sxs-lookup"><span data-stu-id="20b09-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="20b09-273">Yukarıdaki satırı ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="20b09-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="20b09-274">İçindeki kod, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> talepler oluştururken AAD güvenlik grupları ve AAD yönetici rolleri arasında ayrım yapmaz.</span><span class="sxs-lookup"><span data-stu-id="20b09-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="20b09-275">Uygulamanın gruplar ve roller arasında ayrım yapmak için `entry.ODataType` gruplar ve roller arasında ne zaman yinelediğini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="20b09-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="20b09-276">Ayrı güvenlik grubu ve rol talepleri oluşturmak için aşağıdakilere benzer bir kod kullanın:</span><span class="sxs-lookup"><span data-stu-id="20b09-276">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="20b09-277">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="20b09-277">User-defined roles</span></span>

<span data-ttu-id="20b09-278">AAD ile kaydedilen bir uygulama, Kullanıcı tanımlı rolleri kullanmak için de yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="20b09-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="20b09-279">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `roles` , bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirteçte alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .</span><span class="sxs-lookup"><span data-stu-id="20b09-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="20b09-280">Aşağıdaki örnek, bir uygulamanın iki rolle yapılandırıldığını varsayar:</span><span class="sxs-lookup"><span data-stu-id="20b09-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="20b09-281">Azure AD Premium hesabı olmadan güvenlik gruplarına roller atayamasanız da, kullanıcıları rollere atayabilir ve `roles` Standart bir Azure hesabı olan kullanıcılar için bir talep alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20b09-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="20b09-282">Bu bölümdeki kılavuz bir Azure AD Premium hesabı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="20b09-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="20b09-283">Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="20b09-284">`roles`AAD tarafından gönderilen tek talep, Kullanıcı tanımlı rolleri `appRoles` `value` bir JSON dizisinde öğeleri olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="20b09-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="20b09-285">Uygulama, rollerin JSON dizisini bağımsız taleplerine dönüştürmelidir `role` .</span><span class="sxs-lookup"><span data-stu-id="20b09-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="20b09-286">`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="20b09-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="20b09-287">`CustomUserFactory` *`Client`* Blazor [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasına veya uygulamasına ekleyin ve kaydedin.</span><span class="sxs-lookup"><span data-stu-id="20b09-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="20b09-288">Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .</span><span class="sxs-lookup"><span data-stu-id="20b09-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="20b09-289">`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya uygulamasında *`Client`* Blazor , `role` rol talebi olarak "" adlı talebi belirtin:</span><span class="sxs-lookup"><span data-stu-id="20b09-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="20b09-290">Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="20b09-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="20b09-291">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="20b09-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="20b09-292">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="20b09-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="20b09-293">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="20b09-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="20b09-294">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="20b09-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="20b09-295">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="20b09-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="20b09-296">AAD yönetici rolü nesne kimlikleri</span><span class="sxs-lookup"><span data-stu-id="20b09-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="20b09-297">Aşağıdaki tabloda sunulan nesne kimlikleri, talepler için [ilkeler](xref:security/authorization/policies) oluşturmak üzere kullanılır `group` .</span><span class="sxs-lookup"><span data-stu-id="20b09-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="20b09-298">İlkeler, bir uygulamanın bir uygulamadaki çeşitli etkinlikler için kullanıcıları yetkilendirmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="20b09-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="20b09-299">Daha fazla bilgi için [Kullanıcı tanımlı gruplar ve AAD yönetici rolleri](#user-defined-groups-and-administrator-roles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="20b09-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="20b09-300">AAD yönetici rolü</span><span class="sxs-lookup"><span data-stu-id="20b09-300">AAD Administrator Role</span></span> | <span data-ttu-id="20b09-301">Nesne Kimliği</span><span class="sxs-lookup"><span data-stu-id="20b09-301">Object ID</span></span>
--- | ---
<span data-ttu-id="20b09-302">Uygulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-302">Application administrator</span></span> | <span data-ttu-id="20b09-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="20b09-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="20b09-304">Uygulama geliştiricisi</span><span class="sxs-lookup"><span data-stu-id="20b09-304">Application developer</span></span> | <span data-ttu-id="20b09-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="20b09-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="20b09-306">Kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-306">Authentication administrator</span></span> | <span data-ttu-id="20b09-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="20b09-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="20b09-308">Azure DevOps Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-308">Azure DevOps administrator</span></span> | <span data-ttu-id="20b09-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="20b09-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="20b09-310">Azure Information Protection Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="20b09-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="20b09-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="20b09-312">B2C ıEF anahtar kümesi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="20b09-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="20b09-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="20b09-314">B2C ıEF Ilke Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="20b09-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="20b09-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="20b09-316">B2C Kullanıcı akış Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-316">B2C user flow administrator</span></span> | <span data-ttu-id="20b09-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="20b09-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="20b09-318">B2C Kullanıcı akışı öznitelik Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="20b09-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="20b09-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="20b09-320">Faturalama yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-320">Billing administrator</span></span> | <span data-ttu-id="20b09-321">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="20b09-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="20b09-322">Bulut uygulaması Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-322">Cloud application administrator</span></span> | <span data-ttu-id="20b09-323">250b5fe3-b553-458d-9A53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="20b09-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="20b09-324">Bulut Cihaz Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-324">Cloud device administrator</span></span> | <span data-ttu-id="20b09-325">26cd4b44-2636-4DDB-BDFA-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="20b09-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="20b09-326">Uyumluluk Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-326">Compliance administrator</span></span> | <span data-ttu-id="20b09-327">9d6e1dd0-c9f8-45F8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="20b09-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="20b09-328">Uyumluluk verileri Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-328">Compliance data administrator</span></span> | <span data-ttu-id="20b09-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="20b09-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="20b09-330">Koşullu Erişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-330">Conditional Access administrator</span></span> | <span data-ttu-id="20b09-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="20b09-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="20b09-332">Müşteri Kasası erişim onaylayıcısı</span><span class="sxs-lookup"><span data-stu-id="20b09-332">Customer LockBox access approver</span></span> | <span data-ttu-id="20b09-333">c18d54a8-b13e-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="20b09-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="20b09-334">Masaüstü Analizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="20b09-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="20b09-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="20b09-336">Dizin okuyucuları</span><span class="sxs-lookup"><span data-stu-id="20b09-336">Directory readers</span></span> | <span data-ttu-id="20b09-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="20b09-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="20b09-338">Dynamics 365 Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="20b09-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="20b09-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="20b09-340">Exchange yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-340">Exchange administrator</span></span> | <span data-ttu-id="20b09-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="20b09-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="20b09-342">Dış Identity sağlayıcı Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-342">External Identity Provider administrator</span></span> | <span data-ttu-id="20b09-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="20b09-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="20b09-344">Genel yönetici</span><span class="sxs-lookup"><span data-stu-id="20b09-344">Global administrator</span></span> | <span data-ttu-id="20b09-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="20b09-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="20b09-346">Genel okuyucu</span><span class="sxs-lookup"><span data-stu-id="20b09-346">Global reader</span></span> | <span data-ttu-id="20b09-347">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="20b09-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="20b09-348">Grup Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-348">Groups administrator</span></span> | <span data-ttu-id="20b09-349">158b3e5a-d89d-460B-92B5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="20b09-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="20b09-350">Konuk davetci</span><span class="sxs-lookup"><span data-stu-id="20b09-350">Guest inviter</span></span> | <span data-ttu-id="20b09-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="20b09-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="20b09-352">Yardım Masası Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-352">Helpdesk administrator</span></span> | <span data-ttu-id="20b09-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="20b09-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="20b09-354">Intune yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-354">Intune administrator</span></span> | <span data-ttu-id="20b09-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="20b09-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="20b09-356">Kaizala Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-356">Kaizala administrator</span></span> | <span data-ttu-id="20b09-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="20b09-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="20b09-358">Lisans yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-358">License administrator</span></span> | <span data-ttu-id="20b09-359">3355458a-E423-44bf-8b98-4ac5e572begin' 5</span><span class="sxs-lookup"><span data-stu-id="20b09-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="20b09-360">İleti Merkezi Gizlilik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="20b09-360">Message center privacy reader</span></span> | <span data-ttu-id="20b09-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="20b09-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="20b09-362">İleti Merkezi okuyucusu</span><span class="sxs-lookup"><span data-stu-id="20b09-362">Message center reader</span></span> | <span data-ttu-id="20b09-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="20b09-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="20b09-364">Office uygulamaları Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-364">Office apps administrator</span></span> | <span data-ttu-id="20b09-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="20b09-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="20b09-366">Parola yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-366">Password administrator</span></span> | <span data-ttu-id="20b09-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="20b09-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="20b09-368">Power BI Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-368">Power BI administrator</span></span> | <span data-ttu-id="20b09-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="20b09-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="20b09-370">Power platformu Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-370">Power platform administrator</span></span> | <span data-ttu-id="20b09-371">76d6f95e-9a15-4D7D-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="20b09-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="20b09-372">Ayrıcalıklı kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-372">Privileged authentication administrator</span></span> | <span data-ttu-id="20b09-373">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="20b09-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="20b09-374">Ayrıcalıklı rol yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-374">Privileged role administrator</span></span> | <span data-ttu-id="20b09-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="20b09-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="20b09-376">Rapor okuyucu</span><span class="sxs-lookup"><span data-stu-id="20b09-376">Reports reader</span></span> | <span data-ttu-id="20b09-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="20b09-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="20b09-378">Yönetici ara</span><span class="sxs-lookup"><span data-stu-id="20b09-378">Search administrator</span></span> | <span data-ttu-id="20b09-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="20b09-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="20b09-380">Arama Düzenleyicisi</span><span class="sxs-lookup"><span data-stu-id="20b09-380">Search editor</span></span> | <span data-ttu-id="20b09-381">6a6858c6-5f0d-44AC-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="20b09-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="20b09-382">Güvenlik yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-382">Security administrator</span></span> | <span data-ttu-id="20b09-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="20b09-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="20b09-384">Güvenlik operatörü</span><span class="sxs-lookup"><span data-stu-id="20b09-384">Security operator</span></span> | <span data-ttu-id="20b09-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="20b09-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="20b09-386">Güvenlik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="20b09-386">Security reader</span></span> | <span data-ttu-id="20b09-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="20b09-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="20b09-388">Hizmet desteği yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-388">Service support administrator</span></span> | <span data-ttu-id="20b09-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="20b09-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="20b09-390">SharePoint yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-390">SharePoint administrator</span></span> | <span data-ttu-id="20b09-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="20b09-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="20b09-392">Skype Kurumsal yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-392">Skype for Business administrator</span></span> | <span data-ttu-id="20b09-393">0a8cee12-e21d-43EF-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="20b09-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="20b09-394">Takımlar Iletişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-394">Teams Communications Administrator</span></span> | <span data-ttu-id="20b09-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="20b09-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="20b09-396">Takımlar Iletişimleri Destek Mühendisi</span><span class="sxs-lookup"><span data-stu-id="20b09-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="20b09-397">802dd94e-d717-46F6-AF98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="20b09-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="20b09-398">Takımlar Iletişim uzmanı</span><span class="sxs-lookup"><span data-stu-id="20b09-398">Teams Communications Specialist</span></span> | <span data-ttu-id="20b09-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="20b09-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="20b09-400">Takımlar Hizmet Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-400">Teams Service Administrator</span></span> | <span data-ttu-id="20b09-401">8846a0be-197b-443A-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="20b09-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="20b09-402">Kullanıcı yöneticisi</span><span class="sxs-lookup"><span data-stu-id="20b09-402">User administrator</span></span> | <span data-ttu-id="20b09-403">1f6eed58-7dd3-460B-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="20b09-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20b09-404">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="20b09-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
