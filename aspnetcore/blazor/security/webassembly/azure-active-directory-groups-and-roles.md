---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
ms.openlocfilehash: 8d18a8f9282e83c3b3ef5b9c7c6651c9b525a21f
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751578"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="97405-103">Azure Active Directory (AAD) grupları, yönetici rolleri ve uygulama rolleri</span><span class="sxs-lookup"><span data-stu-id="97405-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="97405-104">Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="97405-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="97405-105">Bu makale, Blazor Microsoft 1,0 ile ASP.NET Core Apps sürüm 3,1 için geçerlidir Identity ve Identity kısa bir süre içinde 2,0 ile 5,0 güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="97405-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="97405-106">Daha fazla bilgi için, aşağıdaki GitHub sorununa ve çekme isteğine bakın.</span><span class="sxs-lookup"><span data-stu-id="97405-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="97405-107">Çekme isteğinin **dosyalar değişti** sekmesi, taslak metnini ve bu makaledeki güncelleştirmelere yönelik örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="97405-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="97405-108">İnceleme ve son güncelleştirmeler sonrasında, çekme isteği canlı belge kümesiyle birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="97405-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="97405-109">Sorun: [ Blazor AAD grupları ve rolleri ile ilgili (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="97405-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="97405-110">Çekme Isteği: [ Blazor AAD grupları ve rolleri konu 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="97405-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="97405-111">Azure Active Directory (AAD), ile birleştirilebilir çeşitli yetkilendirme yaklaşımları sağlar ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="97405-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="97405-112">Gruplar</span><span class="sxs-lookup"><span data-stu-id="97405-112">Groups</span></span>
  * <span data-ttu-id="97405-113">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="97405-113">Security</span></span>
  * <span data-ttu-id="97405-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="97405-114">Microsoft 365</span></span>
  * <span data-ttu-id="97405-115">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="97405-115">Distribution</span></span>
* <span data-ttu-id="97405-116">Roller</span><span class="sxs-lookup"><span data-stu-id="97405-116">Roles</span></span>
  * <span data-ttu-id="97405-117">AAD yönetici rolleri</span><span class="sxs-lookup"><span data-stu-id="97405-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="97405-118">Uygulama rolleri</span><span class="sxs-lookup"><span data-stu-id="97405-118">App Roles</span></span>

<span data-ttu-id="97405-119">Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="97405-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="97405-120">Microsoft Hesapları ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="97405-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="97405-121">AAD ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="97405-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="97405-122">AAD ile barındırılan</span><span class="sxs-lookup"><span data-stu-id="97405-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="97405-123">Makalenin Kılavuzu, istemci ve sunucu uygulamaları için yönergeler sağlar:</span><span class="sxs-lookup"><span data-stu-id="97405-123">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="97405-124">**İstemci**: tek başına Blazor WebAssembly uygulamalar veya *`Client`* barındırılan bir çözümün uygulaması Blazor .</span><span class="sxs-lookup"><span data-stu-id="97405-124">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="97405-125">**Sunucu**: tek başına ASP.NET Core Server API/Web API Apps veya *`Server`* barındırılan bir çözümün uygulaması Blazor .</span><span class="sxs-lookup"><span data-stu-id="97405-125">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="97405-126">Kapsamlar</span><span class="sxs-lookup"><span data-stu-id="97405-126">Scopes</span></span>

<span data-ttu-id="97405-127">Kullanıcı profili, rol ataması ve grup üyeliği verilerine yönelik [MICROSOFT Graph API](/graph/use-the-api) çağrılarına izin vermek Için, **istemci** `https://graph.microsoft.com/User.Read` Azure Portal () [Graph API izin (kapsam)](/graph/permissions-reference) ile yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="97405-127">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="97405-128">Rol ve grup üyeliği verileri için Graph API çağıran bir **sunucu** uygulaması, `GroupMember.Read.All` `https://graph.microsoft.com/GroupMember.Read.All` Azure Portal () [Graph API izin (kapsam)](/graph/permissions-reference) olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="97405-128">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="97405-129">Bu kapsamlar, bu makalenin ilk bölümünde listelenen konularda açıklanan AAD dağıtım senaryolarında gerekli olan kapsamlara ek olarak gereklidir.</span><span class="sxs-lookup"><span data-stu-id="97405-129">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="97405-130">"Permission" ve "scope" sözcükleri, Azure portal ve çeşitli Microsoft ve dış belge kümelerinde birbirinin yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="97405-130">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="97405-131">Bu makalede, Azure portal bir uygulamaya atanan izinler için "scope" sözcüğü kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="97405-131">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="97405-132">Grup üyeliği talepler özniteliği</span><span class="sxs-lookup"><span data-stu-id="97405-132">Group Membership Claims attribute</span></span>

<span data-ttu-id="97405-133">**İstemci** ve **sunucu** uygulamaları için Azure Portal uygulama bildiriminde, [ `groupMembershipClaims` özniteliğini](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) olarak ayarlayın `All` .</span><span class="sxs-lookup"><span data-stu-id="97405-133">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="97405-134">Bir değeri, `All` oturum açan kullanıcının üyesi olduğu tüm güvenlik gruplarını, dağıtım gruplarını ve rolleri elde eden bir sonuç değeridir.</span><span class="sxs-lookup"><span data-stu-id="97405-134">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="97405-135">Uygulamanın Azure portal kaydını açın.</span><span class="sxs-lookup"><span data-stu-id="97405-135">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="97405-136">  >  Kenar çubuğunda **bildirimi** Yönet ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="97405-136">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="97405-137">Özniteliği bulun `groupMembershipClaims` .</span><span class="sxs-lookup"><span data-stu-id="97405-137">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="97405-138">Değeri `All` olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="97405-138">Set the value to `All`.</span></span>
1. <span data-ttu-id="97405-139">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="97405-139">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="97405-140">Özel Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="97405-140">Custom user account</span></span>

<span data-ttu-id="97405-141">Kullanıcıları AAD güvenlik gruplarına ve AAD yönetici rollerine Azure portal atayın.</span><span class="sxs-lookup"><span data-stu-id="97405-141">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="97405-142">Bu makaledeki örnekler:</span><span class="sxs-lookup"><span data-stu-id="97405-142">The examples in this article:</span></span>

* <span data-ttu-id="97405-143">Bir kullanıcının, sunucu API verilerine erişim yetkilendirmesi için Azure portal AAD kiracısında AAD *faturalandırma Yöneticisi* rolüne atandığını varsayın.</span><span class="sxs-lookup"><span data-stu-id="97405-143">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="97405-144">**İstemci** ve **sunucu** uygulamaları içindeki erişimi denetlemek için [Yetkilendirme İlkeleri](xref:security/authorization/policies) kullanın.</span><span class="sxs-lookup"><span data-stu-id="97405-144">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="97405-145">**İstemci** uygulamasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> için Özellikleri Ekle ' ye genişletin:</span><span class="sxs-lookup"><span data-stu-id="97405-145">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="97405-146">`Roles`: AAD uygulama rolleri dizisi ( [uygulama rolleri](#app-roles) bölümünde ele alınmıştır)</span><span class="sxs-lookup"><span data-stu-id="97405-146">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="97405-147">`Wids`: [İyi bilinen kimlik talepleri ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims) Için AAD yönetici rolleri</span><span class="sxs-lookup"><span data-stu-id="97405-147">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="97405-148">`Oid`: Değişmez [nesne tanımlayıcı talebi ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (bir kullanıcıyı kiracılar içinde ve kiracılarda benzersiz olarak tanımlar)</span><span class="sxs-lookup"><span data-stu-id="97405-148">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="97405-149">Her dizi özelliğine boş bir dizi atayın, `null` Bu özellikler Döngülerde kullanıldığında gerekli değildir `foreach` .</span><span class="sxs-lookup"><span data-stu-id="97405-149">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="97405-150">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="97405-150">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="97405-151">İçin **istemci** uygulamasının proje dosyasına bir paket başvurusu ekleyin [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="97405-151">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="97405-152">Graph SDK yardımcı program sınıflarını ve yapılandırmasını makalenin *Graph SDK* bölümüne ekleyin <xref:blazor/security/webassembly/graph-api#graph-sdk> .</span><span class="sxs-lookup"><span data-stu-id="97405-152">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="97405-153">`GraphClientExtensions`Sınıfında, `User.Read` yöntemi içindeki erişim belirtecinin kapsamını belirtin `AuthenticateRequestAsync` :</span><span class="sxs-lookup"><span data-stu-id="97405-153">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="97405-154">Aşağıdaki özel kullanıcı hesabı fabrikasını **istemci** uygulamasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="97405-154">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="97405-155">Özel Kullanıcı fabrikası oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="97405-155">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="97405-156">Uygulama rolü talepleri ( `appRole` ) ( [uygulama rolleri](#app-roles) bölümünde açıklanmıştır)</span><span class="sxs-lookup"><span data-stu-id="97405-156">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="97405-157">AAD yönetici rolü talepleri ( `directoryRole` )</span><span class="sxs-lookup"><span data-stu-id="97405-157">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="97405-158">Kullanıcının cep telefonu numarası () için örnek bir kullanıcı profili veri talebi `mobilePhone`</span><span class="sxs-lookup"><span data-stu-id="97405-158">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="97405-159">AAD grup talepleri ( `directoryGroup` )</span><span class="sxs-lookup"><span data-stu-id="97405-159">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="97405-160">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="97405-160">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
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
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
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

<span data-ttu-id="97405-161">Yukarıdaki kod geçişli üyelikleri içermez.</span><span class="sxs-lookup"><span data-stu-id="97405-161">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="97405-162">Uygulama doğrudan ve geçişli grup üyeliği talepleri gerektiriyorsa, ( `MemberOf` `IUserMemberOfCollectionWithReferencesRequestBuilder` ) özelliğini () ile değiştirin `TransitiveMemberOf` `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` .</span><span class="sxs-lookup"><span data-stu-id="97405-162">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="97405-163">Yukarıdaki kod, `groups` `#microsoft.graph.directoryRole` Microsoft platformu 2,0 tarafından döndürülen GUID değerleri, Identity [**rol şablonu kimlikleri**](/azure/active-directory/roles/permissions-reference#role-template-ids)değil AAD yönetici rolü **varlık kimlikleri** olduğundan, AAD yönetici rolleri (türü) olan grup üyeliği taleplerini yok sayar.</span><span class="sxs-lookup"><span data-stu-id="97405-163">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="97405-164">Varlık kimlikleri Microsoft platformu 2,0 ' deki kiracılar arasında kararlı değildir Identity ve uygulamalardaki kullanıcılara yönelik yetkilendirme ilkeleri oluşturmak için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="97405-164">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="97405-165">**`wids` Talepler tarafından sunulan** AAD yönetici rolleri Için her zaman **rol şablonu kimliklerini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="97405-165">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="97405-166">`Program.Main` **İstemci** uygulamada, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="97405-166">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="97405-167">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="97405-167">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="97405-168">Yetkilendirme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="97405-168">Authorization configuration</span></span>

<span data-ttu-id="97405-169">**İstemci** uygulamasında, Içindeki her [uygulama rolü](#app-roles), AAD yönetici rolü veya güvenlik grubu için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="97405-169">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="97405-170">Aşağıdaki örnek AAD *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="97405-170">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="97405-171">AAD Yönetici rollerinin kimliklerinin tamamı listesi için bkz. Azure belgelerindeki [rol şablonu kimlikleri](/azure/active-directory/roles/permissions-reference#role-template-ids) .</span><span class="sxs-lookup"><span data-stu-id="97405-171">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="97405-172">Yetkilendirme ilkeleri hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..</span><span class="sxs-lookup"><span data-stu-id="97405-172">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="97405-173">Aşağıdaki örneklerde, **istemci** uygulaması kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="97405-173">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="97405-174">[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="97405-174">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="97405-175">Bir bileşenin tamamına erişim bir [ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) () kullanılarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :</span><span class="sxs-lookup"><span data-stu-id="97405-175">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="97405-176">Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="97405-176">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="97405-177">Bir ilke denetimi, [yordamsal Logic ile kodda da gerçekleştirilebilir](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="97405-177">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="97405-178">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="97405-178">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="97405-179">Sunucu API/Web API erişimi yetkilendir</span><span class="sxs-lookup"><span data-stu-id="97405-179">Authorize server API/web API access</span></span>

<span data-ttu-id="97405-180">Bir **sunucu** API 'si uygulaması, bir erişim belirteci,, ve talepleri içerdiğinde güvenlik GRUPLARı, AAD yönetici rolleri ve uygulama rolleri için [YETKILENDIRME ilkeleriyle](xref:security/authorization/policies) güvenli API uç noktalarına erişmek için kullanıcılara yetki verebilir `groups` `wids` `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` .</span><span class="sxs-lookup"><span data-stu-id="97405-180">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="97405-181">Aşağıdaki örnek,  `Startup.ConfigureServices` `wids` (Iyi bilinen kimlikler/rol şablonu KIMLIKLERI) taleplerini kullanarak AAD faturalandırma Yöneticisi rolü için bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="97405-181">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="97405-182">AAD Yönetici rollerinin kimliklerinin tamamı listesi için bkz. Azure belgelerindeki [rol şablonu kimlikleri](/azure/active-directory/roles/permissions-reference#role-template-ids) .</span><span class="sxs-lookup"><span data-stu-id="97405-182">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="97405-183">Yetkilendirme ilkeleri hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..</span><span class="sxs-lookup"><span data-stu-id="97405-183">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="97405-184">**Sunucu** uygulamasındaki bir denetleyiciye erişim, ilke adı ile bir [ `[Authorize]` özniteliği](xref:security/authorization/simple) kullanmayı temel alabilir (API belgeleri: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).</span><span class="sxs-lookup"><span data-stu-id="97405-184">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="97405-185">Aşağıdaki örnek, ' dan `BillingDataController` Azure Faturalandırma yöneticileri ' nden bir ilke adı ile yapılan faturalandırma verilerine erişimi kısıtlar `BillingAdministrator` :</span><span class="sxs-lookup"><span data-stu-id="97405-185">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="97405-186">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="97405-186">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="97405-187">Uygulama rolleri</span><span class="sxs-lookup"><span data-stu-id="97405-187">App Roles</span></span>

<span data-ttu-id="97405-188">Uygulama rolleri üyelik talepleri sağlamak üzere Azure portal uygulamayı yapılandırmak için, bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirtece alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .</span><span class="sxs-lookup"><span data-stu-id="97405-188">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="97405-189">Aşağıdaki örnek, **istemci** ve **sunucu** uygulamalarının iki rolle yapılandırıldığını ve rollerin bir test kullanıcısına atandığını varsayar:</span><span class="sxs-lookup"><span data-stu-id="97405-189">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="97405-190">Barındırılan bir Blazor WebAssembly uygulama veya tek başına uygulamalar (tek başına bir Blazor WebAssembly uygulama ve tek başına bir ASP.NET Core Server API/Web API uygulaması) geliştirirken, `appRoles` hem istemcinin hem de sunucu Azure Portal uygulama kayıtlarının bildirim özelliği aynı yapılandırılmış rolleri içermelidir.</span><span class="sxs-lookup"><span data-stu-id="97405-190">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="97405-191">İstemci uygulamasının bildiriminde rolleri kurduktan sonra, bunları sunucu uygulamasının bildirimine tamamen kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="97405-191">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="97405-192">`appRoles`İstemci ve sunucu uygulama kayıtları arasında bildirimi yansıtmıyorsanız, erişim belirteçlerinin doğru rol taleplerine sahip olsa bile, sunucu API/Web API 'sinin kimliği doğrulanmış kullanıcıları için rol talepleri kurulmaz.</span><span class="sxs-lookup"><span data-stu-id="97405-192">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="97405-193">Azure AD Premium hesabı olmadan gruplara roller atayamıyorum, ancak kullanıcılara roller atayabilir ve standart bir Azure hesabı olan kullanıcılar için bir rol talebi alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="97405-193">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="97405-194">Bu bölümdeki kılavuz bir AAD Premium hesabı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="97405-194">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="97405-195">Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.</span><span class="sxs-lookup"><span data-stu-id="97405-195">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="97405-196">`CustomAccountFactory` [Özel Kullanıcı hesabı](#custom-user-account) bölümünde GÖSTERILEN, bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="97405-196">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="97405-197">`CustomAccountFactory` **Istemci** uygulamasına [Özel Kullanıcı hesabı](#custom-user-account) bölümünde gösterildiği gibi ekleyin ve kaydedin.</span><span class="sxs-lookup"><span data-stu-id="97405-197">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="97405-198">Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .</span><span class="sxs-lookup"><span data-stu-id="97405-198">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="97405-199">`Program.Main`Bir **istemci** uygulamasında, `appRole` denetimler için rol talebi olarak "" adlı talebi belirtin <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="97405-199">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="97405-200">`directoryRoles`Talebi (yönetici ROLLERI Ekle) kullanmayı tercih ediyorsanız, ' a atayın `directoryRoles` <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="97405-200">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="97405-201">`Startup.ConfigureServices` **Sunucu** uygulamasında, `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` denetimler için rol talebi olarak "" adlı talebi belirtin <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="97405-201">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="97405-202">`wids`Talebi (yönetici ROLLERI Ekle) kullanmayı tercih ediyorsanız, ' a atayın `wids` <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="97405-202">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="97405-203">Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="97405-203">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="97405-204">**İstemci** uygulamasının bileşenlerinde yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="97405-204">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="97405-205">`AuthorizeView` bileşeninde</span><span class="sxs-lookup"><span data-stu-id="97405-205">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="97405-206">[ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="97405-206">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="97405-207">Yordamsal mantık</span><span class="sxs-lookup"><span data-stu-id="97405-207">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="97405-208">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="97405-208">Multiple role tests are supported:</span></span>

* <span data-ttu-id="97405-209">Kullanıcının **,** `admin`  `developer` bileşeni olan veya rolünde olması gerekir `AuthorizeView` :</span><span class="sxs-lookup"><span data-stu-id="97405-209">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="97405-210">Kullanıcının **hem** hem de `admin`  `developer` bileşen ile rollerinin olmasını gerektir `AuthorizeView` :</span><span class="sxs-lookup"><span data-stu-id="97405-210">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="97405-211">Kullanıcının  `admin`  `developer` özniteliği olan veya rolünde olması gerekir `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="97405-211">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="97405-212">Kullanıcının **hem** hem de şu `admin`  `developer` özniteliği olan roller içinde olmasını gerektir `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="97405-212">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="97405-213">Kullanıcının **,** `admin`  `developer` yordamsal kodu olan veya rolünde olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="97405-213">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="97405-214"> `admin`  `developer` Önceki örnekteki [koşullu veya ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) öğesini [koşullu ve ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) olarak değiştirerek, kullanıcının hem hem de yordamsal kodu bulunan rollerde olmasını gerektir:</span><span class="sxs-lookup"><span data-stu-id="97405-214">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="97405-215">**Sunucu** uygulamasının denetleyicilerde yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="97405-215">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="97405-216">[ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="97405-216">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="97405-217">Yordamsal mantık</span><span class="sxs-lookup"><span data-stu-id="97405-217">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="97405-218">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="97405-218">Multiple role tests are supported:</span></span>

* <span data-ttu-id="97405-219">Kullanıcının  `admin`  `developer` özniteliği olan veya rolünde olması gerekir `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="97405-219">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="97405-220">Kullanıcının **hem** hem de şu `admin`  `developer` özniteliği olan roller içinde olmasını gerektir `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="97405-220">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="97405-221">Kullanıcının **,** `admin`  `developer` yordamsal kodu olan veya rolünde olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="97405-221">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="97405-222"> `admin`  `developer` Önceki örnekteki [koşullu veya ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) öğesini [koşullu ve ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) olarak değiştirerek, kullanıcının hem hem de yordamsal kodu bulunan rollerde olmasını gerektir:</span><span class="sxs-lookup"><span data-stu-id="97405-222">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="97405-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="97405-223">Additional resources</span></span>

* [<span data-ttu-id="97405-224">Rol şablonu kimlikleri (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="97405-224">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="97405-225">`groupMembershipClaims` öznitelik (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="97405-225">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="97405-226">Nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="97405-226">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="97405-227">Uygulama rolleri (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="97405-227">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
