---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402162"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="1ed73-103">Azure AD grupları, yönetim rolleri ve Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="1ed73-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="1ed73-104">Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1ed73-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="1ed73-105">Azure Active Directory (AAD), ASP.NET Core birleştirilebilecek çeşitli yetkilendirme yaklaşımları sağlar Identity :</span><span class="sxs-lookup"><span data-stu-id="1ed73-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1ed73-106">Kullanıcı tanımlı gruplar</span><span class="sxs-lookup"><span data-stu-id="1ed73-106">User-defined groups</span></span>
  * <span data-ttu-id="1ed73-107">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="1ed73-107">Security</span></span>
  * <span data-ttu-id="1ed73-108">O365</span><span class="sxs-lookup"><span data-stu-id="1ed73-108">O365</span></span>
  * <span data-ttu-id="1ed73-109">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="1ed73-109">Distribution</span></span>
* <span data-ttu-id="1ed73-110">Roller</span><span class="sxs-lookup"><span data-stu-id="1ed73-110">Roles</span></span>
  * <span data-ttu-id="1ed73-111">Yerleşik yönetim rolleri</span><span class="sxs-lookup"><span data-stu-id="1ed73-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="1ed73-112">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="1ed73-112">User-defined roles</span></span>

<span data-ttu-id="1ed73-113">Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="1ed73-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="1ed73-114">Microsoft Hesapları ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="1ed73-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1ed73-115">AAD ile bağımsız</span><span class="sxs-lookup"><span data-stu-id="1ed73-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1ed73-116">AAD ile barındırılan</span><span class="sxs-lookup"><span data-stu-id="1ed73-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="1ed73-117">Kullanıcı tanımlı gruplar ve yerleşik yönetim rolleri</span><span class="sxs-lookup"><span data-stu-id="1ed73-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="1ed73-118">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="1ed73-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="1ed73-119">Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve yerleşik yönetici rollerine atayın.</span><span class="sxs-lookup"><span data-stu-id="1ed73-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="1ed73-120">Azure AD güvenlik gruplarını kullanan roller</span><span class="sxs-lookup"><span data-stu-id="1ed73-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="1ed73-121">`groupMembershipClaims`özniteliğe</span><span class="sxs-lookup"><span data-stu-id="1ed73-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="1ed73-122">Aşağıdaki örneklerde, bir kullanıcının AAD yerleşik *Faturalama yöneticisi* rolüne atandığını varsayılır.</span><span class="sxs-lookup"><span data-stu-id="1ed73-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="1ed73-123">`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="1ed73-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="1ed73-124">Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1ed73-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="1ed73-125"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Gruplar ve roller için dizi özelliklerini içerecek şekilde genişletin.</span><span class="sxs-lookup"><span data-stu-id="1ed73-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="1ed73-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="1ed73-126">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="1ed73-127">Barındırılan bir çözümün tek başına uygulamasında veya Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1ed73-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="1ed73-128">Aşağıdaki fabrika Ayrıca `roles` , [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde kapsanan talep dizilerini işleyecek şekilde yapılandırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="1ed73-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
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

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="1ed73-129">Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `groups` .</span><span class="sxs-lookup"><span data-stu-id="1ed73-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1ed73-130">`Program.Main` `Program.cs` Bir barındırılan çözümün tek başına uygulamasının veya istemci uygulamasının fabrikasını () kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1ed73-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="1ed73-131">İçindeki her grup veya rol için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="1ed73-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="1ed73-132">Aşağıdaki örnek, AAD yerleşik *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1ed73-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1ed73-133">AAD rolü nesne kimliklerinin tamamı listesi için bkz. [AAD yönetim rolü grubu kimlikleri](#aad-adminstrative-role-group-ids) bölümü.</span><span class="sxs-lookup"><span data-stu-id="1ed73-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="1ed73-134">Aşağıdaki örneklerde, uygulama kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="1ed73-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="1ed73-135">[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1ed73-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="1ed73-136">Bir bileşenin tamamına erişim, [ `[Authorize]` öznitelik yönergesini](xref:blazor/security/index#authorize-attribute) () kullanarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :</span><span class="sxs-lookup"><span data-stu-id="1ed73-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="1ed73-137">Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1ed73-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="1ed73-138">Bir ilke denetimi, [yordamsal mantığa kodunda de gerçekleştirilebilir](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="1ed73-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="1ed73-139">Kullanıcı tanımlı roller</span><span class="sxs-lookup"><span data-stu-id="1ed73-139">User-defined roles</span></span>

<span data-ttu-id="1ed73-140">AAD ile kaydedilen bir uygulama, Kullanıcı tanımlı rolleri kullanmak için de yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="1ed73-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="1ed73-141">Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `roles` , bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirteçte alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .</span><span class="sxs-lookup"><span data-stu-id="1ed73-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="1ed73-142">Aşağıdaki örnek, bir uygulamanın iki rolle yapılandırıldığını varsayar:</span><span class="sxs-lookup"><span data-stu-id="1ed73-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="1ed73-143">Azure AD Premium hesabı olmadan güvenlik gruplarına roller atayamasanız da, kullanıcıları rollere atayabilir ve `roles` Standart bir Azure hesabı olan kullanıcılar için bir talep alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1ed73-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="1ed73-144">Bu bölümdeki kılavuz bir Azure AD Premium hesabı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1ed73-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="1ed73-145">Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.</span><span class="sxs-lookup"><span data-stu-id="1ed73-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="1ed73-146">`roles`AAD tarafından gönderilen tek talep, Kullanıcı tanımlı rolleri `appRoles` `value` bir JSON dizisinde öğeleri olarak sunar.</span><span class="sxs-lookup"><span data-stu-id="1ed73-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="1ed73-147">Uygulama, rollerin JSON dizisini bağımsız taleplerine dönüştürmelidir `role` .</span><span class="sxs-lookup"><span data-stu-id="1ed73-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="1ed73-148">`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1ed73-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="1ed73-149">`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetici rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasını veya istemci uygulamasını ekleyin ve kaydedin.</span><span class="sxs-lookup"><span data-stu-id="1ed73-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="1ed73-150">Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .</span><span class="sxs-lookup"><span data-stu-id="1ed73-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1ed73-151">`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya istemci uygulamasında, rol talebi olarak "" adlı talebi belirtin `role` :</span><span class="sxs-lookup"><span data-stu-id="1ed73-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="1ed73-152">Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="1ed73-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="1ed73-153">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="1ed73-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="1ed73-154">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="1ed73-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="1ed73-155">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="1ed73-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="1ed73-156">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="1ed73-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="1ed73-157">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="1ed73-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="1ed73-158">AAD yönetim rolü grup kimlikleri</span><span class="sxs-lookup"><span data-stu-id="1ed73-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="1ed73-159">Aşağıdaki tabloda sunulan nesne kimlikleri, talepler için [ilkeler](xref:security/authorization/policies) oluşturmak üzere kullanılır `group` .</span><span class="sxs-lookup"><span data-stu-id="1ed73-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="1ed73-160">İlkeler, bir uygulamanın bir uygulamadaki çeşitli etkinlikler için kullanıcıları yetkilendirmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="1ed73-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="1ed73-161">Daha fazla bilgi için [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="1ed73-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="1ed73-162">AAD yönetim rolü</span><span class="sxs-lookup"><span data-stu-id="1ed73-162">AAD Administrative Role</span></span> | <span data-ttu-id="1ed73-163">Nesne Kimliği</span><span class="sxs-lookup"><span data-stu-id="1ed73-163">Object ID</span></span>
--- | ---
<span data-ttu-id="1ed73-164">Uygulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-164">Application administrator</span></span> | <span data-ttu-id="1ed73-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="1ed73-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="1ed73-166">Uygulama geliştiricisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-166">Application developer</span></span> | <span data-ttu-id="1ed73-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="1ed73-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="1ed73-168">Kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-168">Authentication administrator</span></span> | <span data-ttu-id="1ed73-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="1ed73-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="1ed73-170">Azure DevOps Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-170">Azure DevOps administrator</span></span> | <span data-ttu-id="1ed73-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="1ed73-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="1ed73-172">Azure Information Protection Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="1ed73-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="1ed73-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="1ed73-174">B2C ıEF anahtar kümesi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="1ed73-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="1ed73-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="1ed73-176">B2C ıEF Ilke Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="1ed73-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="1ed73-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="1ed73-178">B2C Kullanıcı akış Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-178">B2C user flow administrator</span></span> | <span data-ttu-id="1ed73-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="1ed73-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="1ed73-180">B2C Kullanıcı akışı öznitelik Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="1ed73-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="1ed73-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="1ed73-182">Faturalama yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-182">Billing administrator</span></span> | <span data-ttu-id="1ed73-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="1ed73-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="1ed73-184">Bulut uygulaması Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-184">Cloud application administrator</span></span> | <span data-ttu-id="1ed73-185">250b5fe3-b553-458d-9A53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="1ed73-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="1ed73-186">Bulut Cihaz Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-186">Cloud device administrator</span></span> | <span data-ttu-id="1ed73-187">26cd4b44-2636-4DDB-BDFA-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="1ed73-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="1ed73-188">Uyumluluk Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-188">Compliance administrator</span></span> | <span data-ttu-id="1ed73-189">9d6e1dd0-c9f8-45F8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="1ed73-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="1ed73-190">Uyumluluk verileri Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-190">Compliance data administrator</span></span> | <span data-ttu-id="1ed73-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="1ed73-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="1ed73-192">Koşullu Erişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-192">Conditional Access administrator</span></span> | <span data-ttu-id="1ed73-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="1ed73-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="1ed73-194">Müşteri Kasası erişim onaylayıcısı</span><span class="sxs-lookup"><span data-stu-id="1ed73-194">Customer LockBox access approver</span></span> | <span data-ttu-id="1ed73-195">c18d54a8-b13e-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="1ed73-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="1ed73-196">Masaüstü Analizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="1ed73-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="1ed73-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="1ed73-198">Dizin okuyucuları</span><span class="sxs-lookup"><span data-stu-id="1ed73-198">Directory readers</span></span> | <span data-ttu-id="1ed73-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="1ed73-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="1ed73-200">Dynamics 365 Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="1ed73-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="1ed73-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="1ed73-202">Exchange yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-202">Exchange administrator</span></span> | <span data-ttu-id="1ed73-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="1ed73-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="1ed73-204">Dış Identity sağlayıcı Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-204">External Identity Provider administrator</span></span> | <span data-ttu-id="1ed73-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="1ed73-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="1ed73-206">Genel yönetici</span><span class="sxs-lookup"><span data-stu-id="1ed73-206">Global administrator</span></span> | <span data-ttu-id="1ed73-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="1ed73-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="1ed73-208">Genel okuyucu</span><span class="sxs-lookup"><span data-stu-id="1ed73-208">Global reader</span></span> | <span data-ttu-id="1ed73-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="1ed73-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="1ed73-210">Grup Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-210">Groups administrator</span></span> | <span data-ttu-id="1ed73-211">158b3e5a-d89d-460B-92B5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="1ed73-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="1ed73-212">Konuk davetci</span><span class="sxs-lookup"><span data-stu-id="1ed73-212">Guest inviter</span></span> | <span data-ttu-id="1ed73-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="1ed73-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="1ed73-214">Yardım Masası Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-214">Helpdesk administrator</span></span> | <span data-ttu-id="1ed73-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="1ed73-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="1ed73-216">Intune yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-216">Intune administrator</span></span> | <span data-ttu-id="1ed73-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="1ed73-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="1ed73-218">Kaizala Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-218">Kaizala administrator</span></span> | <span data-ttu-id="1ed73-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="1ed73-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="1ed73-220">Lisans yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-220">License administrator</span></span> | <span data-ttu-id="1ed73-221">3355458a-E423-44bf-8b98-4ac5e572begin' 5</span><span class="sxs-lookup"><span data-stu-id="1ed73-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="1ed73-222">İleti Merkezi Gizlilik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="1ed73-222">Message center privacy reader</span></span> | <span data-ttu-id="1ed73-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="1ed73-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="1ed73-224">İleti Merkezi okuyucusu</span><span class="sxs-lookup"><span data-stu-id="1ed73-224">Message center reader</span></span> | <span data-ttu-id="1ed73-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="1ed73-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="1ed73-226">Office uygulamaları Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-226">Office apps administrator</span></span> | <span data-ttu-id="1ed73-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="1ed73-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="1ed73-228">Parola yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-228">Password administrator</span></span> | <span data-ttu-id="1ed73-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="1ed73-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="1ed73-230">Power BI Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-230">Power BI administrator</span></span> | <span data-ttu-id="1ed73-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="1ed73-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="1ed73-232">Power platformu Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-232">Power platform administrator</span></span> | <span data-ttu-id="1ed73-233">76d6f95e-9a15-4D7D-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="1ed73-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="1ed73-234">Ayrıcalıklı kimlik doğrulama Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-234">Privileged authentication administrator</span></span> | <span data-ttu-id="1ed73-235">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="1ed73-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="1ed73-236">Ayrıcalıklı rol yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-236">Privileged role administrator</span></span> | <span data-ttu-id="1ed73-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="1ed73-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="1ed73-238">Rapor okuyucu</span><span class="sxs-lookup"><span data-stu-id="1ed73-238">Reports reader</span></span> | <span data-ttu-id="1ed73-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="1ed73-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="1ed73-240">Yönetici ara</span><span class="sxs-lookup"><span data-stu-id="1ed73-240">Search administrator</span></span> | <span data-ttu-id="1ed73-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="1ed73-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="1ed73-242">Arama Düzenleyicisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-242">Search editor</span></span> | <span data-ttu-id="1ed73-243">6a6858c6-5f0d-44AC-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="1ed73-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="1ed73-244">Güvenlik yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-244">Security administrator</span></span> | <span data-ttu-id="1ed73-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="1ed73-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="1ed73-246">Güvenlik operatörü</span><span class="sxs-lookup"><span data-stu-id="1ed73-246">Security operator</span></span> | <span data-ttu-id="1ed73-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="1ed73-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="1ed73-248">Güvenlik okuyucusu</span><span class="sxs-lookup"><span data-stu-id="1ed73-248">Security reader</span></span> | <span data-ttu-id="1ed73-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="1ed73-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="1ed73-250">Hizmet desteği yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-250">Service support administrator</span></span> | <span data-ttu-id="1ed73-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="1ed73-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="1ed73-252">SharePoint yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-252">SharePoint administrator</span></span> | <span data-ttu-id="1ed73-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="1ed73-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="1ed73-254">Skype Kurumsal yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-254">Skype for Business administrator</span></span> | <span data-ttu-id="1ed73-255">0a8cee12-e21d-43EF-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="1ed73-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="1ed73-256">Takımlar Iletişim Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-256">Teams Communications Administrator</span></span> | <span data-ttu-id="1ed73-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="1ed73-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="1ed73-258">Takımlar Iletişimleri Destek Mühendisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="1ed73-259">802dd94e-d717-46F6-AF98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="1ed73-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="1ed73-260">Takımlar Iletişim uzmanı</span><span class="sxs-lookup"><span data-stu-id="1ed73-260">Teams Communications Specialist</span></span> | <span data-ttu-id="1ed73-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="1ed73-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="1ed73-262">Takımlar Hizmet Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-262">Teams Service Administrator</span></span> | <span data-ttu-id="1ed73-263">8846a0be-197b-443A-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="1ed73-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="1ed73-264">Kullanıcı yöneticisi</span><span class="sxs-lookup"><span data-stu-id="1ed73-264">User administrator</span></span> | <span data-ttu-id="1ed73-265">1f6eed58-7dd3-460B-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="1ed73-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ed73-266">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1ed73-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
