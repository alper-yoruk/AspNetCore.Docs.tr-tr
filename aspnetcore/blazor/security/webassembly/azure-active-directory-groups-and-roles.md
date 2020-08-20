---
title: Blazor WebAssemblyAzure Active Directory grupları ve rolleriyle ASP.NET Core
author: guardrex
description: Blazor WebAssemblyAzure Active Directory grupları ve rolleri kullanmak için nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
ms.openlocfilehash: 50d019897310f9edd560069137ab73b39d780be8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626239"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD grupları, yönetim rolleri ve Kullanıcı tanımlı roller

Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD), ile birleştirilebilir çeşitli yetkilendirme yaklaşımları sağlar ASP.NET Core Identity :

* Kullanıcı tanımlı gruplar
  * Güvenlik
  * O365
  * Dağıtım
* Roller
  * Yerleşik yönetim rolleri
  * Kullanıcı tanımlı roller

Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:

* [Microsoft Hesapları ile bağımsız](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD ile bağımsız](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD ile barındırılan](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph API izni

Beş taneden fazla yerleşik AAD yönetici rolü ve güvenlik grubu üyeliğine sahip tüm uygulama kullanıcıları için [MICROSOFT Graph API](/graph/use-the-api) çağrısı gerekir.

Graph API çağrılarına izin vermek için, Azure portal bir barındırılan çözümün tek başına veya istemci uygulamasına Blazor aşağıdaki [Graph API izinlerinden](/graph/permissions-reference) birini verin:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` en az ayrıcalıklı izindir ve bu makalede açıklanan örnek için kullanılan izindir.

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>Kullanıcı tanımlı gruplar ve yerleşik yönetim rolleri

Azure portal bir üyelik talebi sağlamak üzere uygulamayı yapılandırmak için `groups` aşağıdaki Azure makalelerine bakın. Kullanıcıları Kullanıcı tanımlı AAD gruplarına ve yerleşik yönetici rollerine atayın.

* [Azure AD güvenlik gruplarını kullanan roller](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` özniteliğe](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Aşağıdaki örneklerde, bir kullanıcının AAD yerleşik *Faturalama yöneticisi* rolüne atandığını varsayılır.

`groups`AAD tarafından gönderilen tek talep, kullanıcının gruplarını ve rollerini BIR JSON dizisinde nesne kimlikleri (GUID 'ler) olarak sunar. Uygulamanın, bir grup ve rol JSON dizisini, `group` uygulamanın [ilke](xref:security/authorization/policies) derleyebilir ayrı talepler olarak dönüştürmesi gerekir.

Atanan yerleşik Azure yönetim rolleri ve Kullanıcı tanımlı grupların sayısı beş aştığında AAD, `hasgroups` `true` talep göndermek yerine değeri olan bir talep gönderir `groups` . En fazla beş rol ve gruba atanmış olan herhangi bir uygulama, bir kullanıcının rollerini ve gruplarını almak için ayrı bir Graph API çağrısı yapmalıdır. Bu makalede belirtilen örnek uygulama, bu senaryoyu ele alınmaktadır. Daha fazla bilgi için bkz `groups` `hasgroups` . [Microsoft Identity platform erişim belirteçleri](/azure/active-directory/develop/access-tokens#payload-claims) 'nde ve talep bilgileri: yük talepleri makalesi.

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

Bir barındırılan çözümün tek başına uygulamasında veya istemci uygulamasında Blazor özel bir <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> sınıf oluşturun. Rol ve grup bilgilerini elde eden Graph API çağrıları için doğru kapsam (izin) kullanın.

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

Rolleri ve grup taleplerini işlemek için özel bir Kullanıcı fabrikası oluşturun. Aşağıdaki örnek uygulama, `roles` [Kullanıcı tanımlı roller](#user-defined-roles) bölümünde ele alınan talep dizisini de işler. Talep varsa `hasgroups` , <xref:System.Net.Http.HttpClient> kullanıcının rollerini ve gruplarını almak için Graph API yetkili bir istek oluşturmak için adı kullanılır. Bu uygulama, Microsoft Identity Platform v 1.0 uç noktasını `https://graph.microsoft.com/v1.0/me/memberOf` ([API belgeleri](/graph/api/user-list-memberof)) kullanır. Bu konudaki kılavuz, Identity msal paketleri v 2.0 için yükseltildiğinde v 2.0 için de güncelleştirilecektir.

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

Varsa, özgün talebi kaldırmak için kod sağlamanız gerekmez, çünkü varsa, `groups` Framework tarafından otomatik olarak kaldırılır.

> [!NOTE]
> Bu örnekteki yaklaşım:
>
> * <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Giden isteklere erişim belirteçleri eklemek için özel bir sınıf ekler.
> * <xref:System.Net.Http.HttpClient>Güvenli, dış Web API uç noktası için Web API istekleri yapmak üzere bir adlandırılmış adı ekler.
> * , <xref:System.Net.Http.HttpClient> Yetkili istekleri yapmak için adını kullanır.
>
> Bu yaklaşım için genel kapsam <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> makalesinde bulabilirsiniz.

`Program.Main` `Program.cs` Bir barındırılan çözümün tek başına uygulamasının veya istemci uygulamasının fabrikasını () kaydedin Blazor . `Directory.Read.All`Uygulama için ek kapsam olarak izin kapsamına onay:

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

[ `AuthorizeView` Bileşen](xref:blazor/security/index#authorizeview-component) ilkeyle birlikte kullanılabilir:

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

`CustomUserFactory` [Kullanıcı tanımlı gruplar ve AAD yerleşik yönetim rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterilen bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır. `CustomUserFactory` Blazor [Kullanıcı TANıMLı gruplar ve AAD yerleşik yönetici rolleri](#user-defined-groups-and-built-in-administrative-roles) bölümünde gösterildiği gibi barındırılan bir çözümün tek başına uygulamasını veya istemci uygulamasını ekleyin ve kaydedin. Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .

`Program.Main`Barındırılan bir çözümün tek başına uygulamasında veya istemci uygulamasında Blazor , `role` rol talebi olarak "" adlı talebi belirtin:

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
