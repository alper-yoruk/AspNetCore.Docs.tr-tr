---
title: BlazorAzure Active Directory grupları ve rolleri ASP.NET Core weelsembly
author: guardrex
description: BlazorAzure Active Directory grupları ve rolleri kullanmak için webassembly 'ı nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 99ebe43da191153aa98cce6bae8fe98035bc7d6f
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103985"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD grupları, yönetim rolleri ve Kullanıcı tanımlı roller

Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD), ASP.NET Core birleştirilebilecek çeşitli yetkilendirme yaklaşımları sağlar Identity :

* Kullanıcı tanımlı gruplar
  * Güvenlik
  * O365
  * Dağıtım
* Roller
  * Yerleşik yönetim rolleri
  * Kullanıcı tanımlı roller

Bu makaledeki kılavuz, Blazor aşağıdaki konularda açıklanan webassembly AAD dağıtım senaryolarına yöneliktir:

* [Microsoft Hesapları ile bağımsız](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD ile bağımsız](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD ile barındırılan](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Kullanıcı tanımlı gruplar ve yerleşik yönetim rolleri

Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın. Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve yerleşik yönetici rollerine atayın.

* [Azure AD güvenlik gruplarını kullanan roller](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [Groupmembershipclaim özniteliği](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Aşağıdaki örneklerde, bir kullanıcının AAD yerleşik *Faturalama yöneticisi* rolüne atandığını varsayılır.

`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar. Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Gruplar ve roller için dizi özelliklerini içerecek şekilde genişletin.

*CustomUserAccount.cs*:

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

Barındırılan bir çözümün tek başına uygulamasında veya Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun. Aşağıdaki fabrika Ayrıca `roles` , [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde kapsanan talep dizilerini işleyecek şekilde yapılandırılmıştır:

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

Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `groups` .

`Program.Main`Bir barındırılan çözümün tek başına uygulamasının veya istemci uygulamasının fabrika konumunu (*program.cs*) kaydedin:

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

İçindeki her grup veya rol için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` . Aşağıdaki örnek, AAD yerleşik *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD rolü nesne kimliklerinin tamamı listesi için bkz. [AAD yönetim rolü grubu kimlikleri](#aad-adminstrative-role-group-ids) bölümü.

Aşağıdaki örneklerde, uygulama kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.

[Authorizeview bileşeni](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:

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

### <a name="user-defined-roles"></a>Kullanıcı tanımlı roller

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

`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır. `CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetici rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasını veya istemci uygulamasını ekleyin ve kaydedin. Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .

`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya istemci uygulamasında, rol talebi olarak "" adlı talebi belirtin `role` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir. Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:

* [Authorizeview bileşeni](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )
* [Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )

  Birden çok rol testi desteklenir:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>AAD yönetim rolü grup kimlikleri

Aşağıdaki tabloda sunulan nesne kimlikleri, talepler için [ilkeler](xref:security/authorization/policies) oluşturmak üzere kullanılır `group` . İlkeler, bir uygulamanın bir uygulamadaki çeşitli etkinlikler için kullanıcıları yetkilendirmesine izin verir. Daha fazla bilgi için [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümüne bakın.

AAD yönetim rolü | Nesne Kimliği
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
