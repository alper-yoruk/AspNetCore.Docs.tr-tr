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
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758268"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Azure Active Directory (AAD) grupları, yönetici rolleri ve uygulama rolleri

Sağlayan, [Luke Latham](https://github.com/guardrex) ve [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD), ile birleştirilebilir çeşitli yetkilendirme yaklaşımları sağlar ASP.NET Core Identity :

* Gruplar
  * Güvenlik
  * Microsoft 365
  * Dağıtım
* Roller
  * AAD yönetici rolleri
  * Uygulama rolleri

Bu makaledeki kılavuz, Blazor WebAssembly aşağıdaki konularda açıklanan AAD dağıtım senaryoları için geçerlidir:

* [Microsoft Hesapları ile bağımsız](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD ile bağımsız](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD ile barındırılan](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

Makalenin Kılavuzu, istemci ve sunucu uygulamaları için yönergeler sağlar:

* **İstemci**: tek başına Blazor WebAssembly uygulamalar veya *`Client`* barındırılan bir çözümün uygulaması Blazor .
* **Sunucu**: tek başına ASP.NET Core Server API/Web API Apps veya *`Server`* barındırılan bir çözümün uygulaması Blazor .

## <a name="scopes"></a>Kapsamlar

Kullanıcı profili, rol ataması ve grup üyeliği verilerine yönelik [MICROSOFT Graph API](/graph/use-the-api) çağrılarına izin vermek Için, **istemci** `https://graph.microsoft.com/User.Read` Azure Portal () [Graph API izin (kapsam)](/graph/permissions-reference) ile yapılandırılır.

Rol ve grup üyeliği verileri için Graph API çağıran bir **sunucu** uygulaması, `GroupMember.Read.All` `https://graph.microsoft.com/GroupMember.Read.All` Azure Portal () [Graph API izin (kapsam)](/graph/permissions-reference) olarak sağlanır.

Bu kapsamlar, bu makalenin ilk bölümünde listelenen konularda açıklanan AAD dağıtım senaryolarında gerekli olan kapsamlara ek olarak gereklidir.

> [!NOTE]
> "Permission" ve "scope" sözcükleri, Azure portal ve çeşitli Microsoft ve dış belge kümelerinde birbirinin yerine kullanılır. Bu makalede, Azure portal bir uygulamaya atanan izinler için "scope" sözcüğü kullanılmaktadır.

## <a name="group-membership-claims-attribute"></a>Grup üyeliği talepler özniteliği

**İstemci** ve **sunucu** uygulamaları için Azure Portal uygulama bildiriminde, [ `groupMembershipClaims` özniteliğini](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) olarak ayarlayın `All` . Bir değeri, `All` oturum açan kullanıcının üyesi olduğu tüm güvenlik gruplarını, dağıtım gruplarını ve rolleri elde eden bir sonuç değeridir.

1. Uygulamanın Azure portal kaydını açın.
1.   >  Kenar çubuğunda **bildirimi** Yönet ' i seçin.
1. Özniteliği bulun `groupMembershipClaims` .
1. Değeri `All` olarak ayarlayın.
1. **Kaydet** düğmesini seçin.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Özel Kullanıcı hesabı

Kullanıcıları AAD güvenlik gruplarına ve AAD yönetici rollerine Azure portal atayın.

Bu makaledeki örnekler:

* Bir kullanıcının, sunucu API verilerine erişim yetkilendirmesi için Azure portal AAD kiracısında AAD *faturalandırma Yöneticisi* rolüne atandığını varsayın.
* **İstemci** ve **sunucu** uygulamaları içindeki erişimi denetlemek için [Yetkilendirme İlkeleri](xref:security/authorization/policies) kullanın.

**İstemci** uygulamasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> için Özellikleri Ekle ' ye genişletin:

* `Roles`: AAD uygulama rolleri dizisi ( [uygulama rolleri](#app-roles) bölümünde ele alınmıştır)
* `Wids`: [İyi bilinen kimlik talepleri ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims) Için AAD yönetici rolleri
* `Oid`: Değişmez [nesne tanımlayıcı talebi ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (bir kullanıcıyı kiracılar içinde ve kiracılarda benzersiz olarak tanımlar)

Her dizi özelliğine boş bir dizi atayın, `null` Bu özellikler Döngülerde kullanıldığında gerekli değildir `foreach` .

`CustomUserAccount.cs`:

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

İçin **istemci** uygulamasının proje dosyasına bir paket başvurusu ekleyin [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Graph SDK yardımcı program sınıflarını ve yapılandırmasını makalenin *Graph SDK* bölümüne ekleyin <xref:blazor/security/webassembly/graph-api#graph-sdk> . `GraphClientExtensions`Sınıfında, `User.Read` yöntemi içindeki erişim belirtecinin kapsamını belirtin `AuthenticateRequestAsync` :

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Aşağıdaki özel kullanıcı hesabı fabrikasını **istemci** uygulamasına ekleyin. Özel Kullanıcı fabrikası oluşturmak için kullanılır:

* Uygulama rolü talepleri ( `appRole` ) ( [uygulama rolleri](#app-roles) bölümünde açıklanmıştır)
* AAD yönetici rolü talepleri ( `directoryRole` )
* Kullanıcının cep telefonu numarası () için örnek bir kullanıcı profili veri talebi `mobilePhone`
* AAD grup talepleri ( `directoryGroup` )

`CustomAccountFactory.cs`:

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

Yukarıdaki kod geçişli üyelikleri içermez. Uygulama doğrudan ve geçişli grup üyeliği talepleri gerektiriyorsa, ( `MemberOf` `IUserMemberOfCollectionWithReferencesRequestBuilder` ) özelliğini () ile değiştirin `TransitiveMemberOf` `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` .

Yukarıdaki kod, `groups` `#microsoft.graph.directoryRole` Microsoft platformu 2,0 tarafından döndürülen GUID değerleri, Identity [**rol şablonu kimlikleri**](/azure/active-directory/roles/permissions-reference#role-template-ids)değil AAD yönetici rolü **varlık kimlikleri** olduğundan, AAD yönetici rolleri (türü) olan grup üyeliği taleplerini yok sayar. Varlık kimlikleri Microsoft platformu 2,0 ' deki kiracılar arasında kararlı değildir Identity ve uygulamalardaki kullanıcılara yönelik yetkilendirme ilkeleri oluşturmak için kullanılmamalıdır. **`wids` Talepler tarafından sunulan** AAD yönetici rolleri Için her zaman **rol şablonu kimliklerini** kullanın.

`Program.Main` **İstemci** uygulamada, msal kimlik doğrulamasını özel kullanıcı hesabı fabrikası kullanacak şekilde yapılandırın.

`Program.cs`:

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

## <a name="authorization-configuration"></a>Yetkilendirme yapılandırması

**İstemci** uygulamasında, Içindeki her [uygulama rolü](#app-roles), AAD yönetici rolü veya güvenlik grubu için bir [ilke](xref:security/authorization/policies) oluşturun `Program.Main` . Aşağıdaki örnek AAD *faturalandırma Yöneticisi* rolü için bir ilke oluşturur:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

AAD Yönetici rollerinin kimliklerinin tamamı listesi için bkz. Azure belgelerindeki [rol şablonu kimlikleri](/azure/active-directory/roles/permissions-reference#role-template-ids) . Yetkilendirme ilkeleri hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..

Aşağıdaki örneklerde, **istemci** uygulaması kullanıcıyı yetkilendirmek için yukarıdaki ilkeyi kullanır.

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

Bir bileşenin tamamına erişim bir [ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) () kullanılarak ilkeye bağlı olabilir <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> :

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Kullanıcı oturum açmadıysa, AAD oturum açma sayfasına yönlendirilir ve oturum açtıktan sonra bileşene geri yönlendirilir.

Bir ilke denetimi, [yordamsal Logic ile kodda da gerçekleştirilebilir](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

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

## <a name="authorize-server-apiweb-api-access"></a>Sunucu API/Web API erişimi yetkilendir

Bir **sunucu** API 'si uygulaması, bir erişim belirteci,, ve talepleri içerdiğinde güvenlik GRUPLARı, AAD yönetici rolleri ve uygulama rolleri için [YETKILENDIRME ilkeleriyle](xref:security/authorization/policies) güvenli API uç noktalarına erişmek için kullanıcılara yetki verebilir `groups` `wids` `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` . Aşağıdaki örnek,  `Startup.ConfigureServices` `wids` (Iyi bilinen kimlikler/rol şablonu KIMLIKLERI) taleplerini kullanarak AAD faturalandırma Yöneticisi rolü için bir ilke oluşturur:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

AAD Yönetici rollerinin kimliklerinin tamamı listesi için bkz. Azure belgelerindeki [rol şablonu kimlikleri](/azure/active-directory/roles/permissions-reference#role-template-ids) . Yetkilendirme ilkeleri hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..

**Sunucu** uygulamasındaki bir denetleyiciye erişim, ilke adı ile bir [ `[Authorize]` özniteliği](xref:security/authorization/simple) kullanmayı temel alabilir (API belgeleri: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).

Aşağıdaki örnek, ' dan `BillingDataController` Azure Faturalandırma yöneticileri ' nden bir ilke adı ile yapılan faturalandırma verilerine erişimi kısıtlar `BillingAdministrator` :

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

Daha fazla bilgi için bkz. <xref:security/authorization/policies>.

## <a name="app-roles"></a>Uygulama rolleri

Uygulama rolleri üyelik talepleri sağlamak üzere Azure portal uygulamayı yapılandırmak için, bkz. [nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları Azure belgelerindeki belirtece alma](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) .

Aşağıdaki örnek, **istemci** ve **sunucu** uygulamalarının iki rolle yapılandırıldığını ve rollerin bir test kullanıcısına atandığını varsayar:

* `admin`
* `developer`

> [!NOTE]
> Barındırılan bir Blazor WebAssembly uygulama veya tek başına uygulamalar (tek başına bir Blazor WebAssembly uygulama ve tek başına bir ASP.NET Core Server API/Web API uygulaması) geliştirirken, `appRoles` hem istemcinin hem de sunucu Azure Portal uygulama kayıtlarının bildirim özelliği aynı yapılandırılmış rolleri içermelidir. İstemci uygulamasının bildiriminde rolleri kurduktan sonra, bunları sunucu uygulamasının bildirimine tamamen kopyalayın. `appRoles`İstemci ve sunucu uygulama kayıtları arasında bildirimi yansıtmıyorsanız, erişim belirteçlerinin doğru rol taleplerine sahip olsa bile, sunucu API/Web API 'sinin kimliği doğrulanmış kullanıcıları için rol talepleri kurulmaz.

> [!NOTE]
> Azure AD Premium hesabı olmadan gruplara roller atayamıyorum, ancak kullanıcılara roller atayabilir ve standart bir Azure hesabı olan kullanıcılar için bir rol talebi alabilirsiniz. Bu bölümdeki kılavuz bir AAD Premium hesabı gerektirmez.
>
> Her ek rol ataması için **_bir kullanıcıyı yeniden ekleyerek_** Azure Portal birden çok rol atanır.

`CustomAccountFactory` [Özel Kullanıcı hesabı](#custom-user-account) bölümünde GÖSTERILEN, bir `roles` JSON dizi değeri olan bir talep üzerinde işlem yapacak şekilde ayarlanır. `CustomAccountFactory` **Istemci** uygulamasına [Özel Kullanıcı hesabı](#custom-user-account) bölümünde gösterildiği gibi ekleyin ve kaydedin. Çerçeve tarafından otomatik olarak kaldırıldığı için özgün talebi kaldırmak üzere kod sağlamanız gerekmez `roles` .

`Program.Main`Bir **istemci** uygulamasında, `appRole` denetimler için rol talebi olarak "" adlı talebi belirtin <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> `directoryRoles`Talebi (yönetici ROLLERI Ekle) kullanmayı tercih ediyorsanız, ' a atayın `directoryRoles` <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .

`Startup.ConfigureServices` **Sunucu** uygulamasında, `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` denetimler için rol talebi olarak "" adlı talebi belirtin <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> :

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
> `wids`Talebi (yönetici ROLLERI Ekle) kullanmayı tercih ediyorsanız, ' a atayın `wids` <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .

Bu noktada bileşen yetkilendirme yaklaşımları işlevseldir. **İstemci** uygulamasının bileşenlerinde yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:

* [`AuthorizeView` bileşeninde](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Yordamsal mantık](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Birden çok rol testi desteklenir:

* Kullanıcının **,** `admin`  `developer` bileşeni olan veya rolünde olması gerekir `AuthorizeView` :

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Kullanıcının **hem** hem de `admin`  `developer` bileşen ile rollerinin olmasını gerektir `AuthorizeView` :

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Kullanıcının  `admin`  `developer` özniteliği olan veya rolünde olması gerekir `[Authorize]` :

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Kullanıcının **hem** hem de şu `admin`  `developer` özniteliği olan roller içinde olmasını gerektir `[Authorize]` :

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Kullanıcının **,** `admin`  `developer` yordamsal kodu olan veya rolünde olması gerekir:

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

*  `admin`  `developer` Önceki örnekteki [koşullu veya ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) öğesini [koşullu ve ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) olarak değiştirerek, kullanıcının hem hem de yordamsal kodu bulunan rollerde olmasını gerektir:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

**Sunucu** uygulamasının denetleyicilerde yetkilendirme mekanizmalarının herhangi biri, `admin` kullanıcıyı yetkilendirmek için rolünü kullanabilir:

* [ `[Authorize]` öznitelik yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Yordamsal mantık](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Birden çok rol testi desteklenir:

* Kullanıcının  `admin`  `developer` özniteliği olan veya rolünde olması gerekir `[Authorize]` :

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Kullanıcının **hem** hem de şu `admin`  `developer` özniteliği olan roller içinde olmasını gerektir `[Authorize]` :

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Kullanıcının **,** `admin`  `developer` yordamsal kodu olan veya rolünde olması gerekir:

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

*  `admin`  `developer` Önceki örnekteki [koşullu veya ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) öğesini [koşullu ve ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) olarak değiştirerek, kullanıcının hem hem de yordamsal kodu bulunan rollerde olmasını gerektir:

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Rol şablonu kimlikleri (Azure belgeleri)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` öznitelik (Azure belgeleri)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma (Azure belgeleri)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Uygulama rolleri (Azure belgeleri)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
