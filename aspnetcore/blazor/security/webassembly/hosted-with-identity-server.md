---
title: Barındırılan bir ASP.NET Core Blazor WebAssembly uygulamasının sunucuyla güvenliğini Identity sağlama
author: guardrex
description: BlazorBir [ Identity sunucu](https://identityserver.io/) arka ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan çözüm oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 87424f413ab21ae51fc1b1b2033069f5a41da566
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818891"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Blazor WebAssemblySunucu ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama Identity

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Bu makalede Blazor , kullanıcıların ve API çağrılarının kimliğini doğrulamak için [ Identity sunucusunu](https://identityserver.io/) kullanan yeni bir barındırılan çözümün nasıl oluşturulacağı açıklanmaktadır.

> [!NOTE]
> Tek başına veya barındırılan bir Blazor WebAssembly uygulamayı var olan bir dış sunucu örneğini kullanacak şekilde yapılandırmak için Identity içindeki yönergeleri izleyin <xref:blazor/security/webassembly/standalone-with-authentication-library> .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:

1. **Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda ** Blazor WebAssembly uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.

1. ASP.NET Core sistemi kullanarak uygulama içinde kullanıcıları depolamak için **Kullanıcı hesaplarını depola ve uygulama Içi** **Kullanıcı hesapları** seçeneğini belirleyin [Identity](xref:security/authentication/identity) .

1. **Gelişmiş** bölümündeki **ASP.NET Core barındırılan** onay kutusunu seçin.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Blazor WebAssemblyBoş bir klasörde kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için, `Individual` `-au|--auth` ASP.NET Core sistemini kullanarak uygulama içinde kullanıcıları depolama seçeneğiyle birlikte kimlik doğrulama mekanizmasını belirtin [Identity](xref:security/authentication/identity) :

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Yer tutucu  | Örnek        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.

Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:

1. **Yeni Blazor WebAssembly uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.

1. Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur [Identity](xref:security/authentication/identity) .

1. **Barındırılan ASP.NET Core** onay kutusunu seçin.

---

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.

### <a name="startup-class"></a>Başlangıç sınıfı

`Startup`Sınıfı aşağıdaki eklemeleri içerir.

* `Startup.ConfigureServices` içinde:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Sunucu üstünde varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan sunucu Identity :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Sunucu tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması Identity :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` içinde:

  * IdentitySunucu ara sunucusu, OpenID Connect (OıDC) uç noktalarını kullanıma sunar:

    ```csharp
    app.UseIdentityServer();
    ```

  * Kimlik doğrulama ara yazılımı, istek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumludur:

    ```csharp
    app.UseAuthentication();
    ```

  * Yetki ara yazılımı yetkilendirme özelliklerini sunar:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>Addadpiauthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Yardımcı yöntemi, ASP.NET Core senaryolar için [ Identity sunucuyu](https://identityserver.io/) yapılandırır. IdentitySunucu, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir. IdentitySunucu, en yaygın senaryolar için gereksiz karmaşıklık sunar. Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır. Kimlik doğrulama gereksinimleriniz değiştikçe, sunucunun tam gücü, Identity kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için kullanılabilir.

### <a name="addno-locidentityserverjwt"></a>IdentityServerjwt Ekle

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, Identity URL alanındaki herhangi bir alt yolda yönlendirilen tüm istekleri işlemeye izin verecek şekilde yapılandırılmıştır Identity `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Diğer tüm istekleri işler. Ayrıca, bu yöntem:

* Bir `{APPLICATION NAME}API` API kaynağını sunucu ile Identity varsayılan kapsamına kaydeder `{APPLICATION NAME}API` .
* Uygulama için sunucu tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır Identity .

### <a name="weatherforecastcontroller"></a>Dalgalı bir denetleyici

`WeatherForecastController`() İçinde `Controllers/WeatherForecastController.cs` , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır. Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir. Varsayılan yetkilendirme ilkesi, tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Yardımcı yöntemi, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> uygulamaya yönelik istekler için varsayılan işleyici olarak yapılandırır.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`() İçinde `Data/ApplicationDbContext.cs` , <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> sunucu için şemayı içerecek şekilde genişletilir Identity . <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflardan birini ve Identity yöntemi metodunu çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

### <a name="oidcconfigurationcontroller"></a>Oıdcconfigurationcontroller

`OidcConfigurationController`() İçinde `Controllers/OidcConfigurationController.cs` , istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.

### <a name="app-settings"></a>Uygulama ayarları

Proje kökündeki uygulama ayarları dosyasında ( `appsettings.json` ), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>İstemci uygulama yapılandırması

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a>`HttpClient`yapılandırmada

`Program.Main`( `Program.cs` ) İçinde, bir adlandırılmış <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ), <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlamak üzere yapılandırılır:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Bir Blazor WebAssembly uygulamayı barındırılan bir çözümün parçası olmayan var olan bir Identity sunucu örneğini kullanacak şekilde yapılandırıyorsanız Blazor , <xref:System.Net.Http.HttpClient> temel adres kaydını <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) konumundan sunucu uygulamasının API yetkilendirme uç noktası URL 'sine değiştirin.

### <a name="api-authorization-support"></a>API yetkilendirme desteği

Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Bu yöntem, uygulamanın mevcut yetkilendirme sistemiyle etkileşimde bulunmak için gereken hizmetleri ayarlar.

```csharp
builder.Services.AddApiAuthorization();
```

Varsayılan olarak, uygulamanın yapılandırması tarafından kural tarafından yüklenir `_configuration/{client-id}` . Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır. Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.

### <a name="imports-file"></a>Dosya içeri aktarmalar

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:

* Kimliği doğrulanmış kullanıcılar için:
  * Geçerli Kullanıcı adını görüntüler.
  * ASP.NET Core 'daki Kullanıcı profili sayfasına bir bağlantı sunar Identity .
  * Uygulamanın oturumunu kapatmak için bir düğme sunar.
* Anonim kullanıcılar için:
  * Kayıt için seçeneği sunar.
  * Oturum açma seçeneği sunar.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Kimlik doğrulama bileşeni

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData bileşeni

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı sunucu projesinden çalıştırın. Visual Studio 'yu kullanırken şunlardan birini yapın:

* Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.
* **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.

## <a name="name-and-role-claim-with-api-authorization"></a>API yetkilendirmesi ile ad ve rol talebi

### <a name="custom-user-factory"></a>Özel Kullanıcı fabrikası

Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun. IdentitySunucu, tek bir talep içinde birden çok rolü JSON dizisi olarak gönderir `role` . Tek bir rol, talepte bir dize değeri olarak gönderilir. Fabrika, `role` kullanıcının rollerinin her biri için ayrı bir talep oluşturur.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

Istemci uygulamasında, () fabrikasını kaydedin `Program.Main` `Program.cs` :

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Sunucu uygulamasında, <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity rol ile ilgili hizmetleri ekleyen Oluşturucu üzerinde çağırın:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>IdentitySunucu yapılandırma

Aşağıdaki yaklaşımlardan **birini** kullanın:

* [API yetkilendirme seçenekleri](#api-authorization-options)
* [Profil hizmeti](#profile-service)

#### <a name="api-authorization-options"></a>API yetkilendirme seçenekleri

Sunucu uygulamasında:

* Identity `name` Ve `role` taleplerini kimlik belirtecine ve erişim belirtecine yerleştirmek için sunucuyu yapılandırın.
* JWT belirteci işleyicisindeki roller için varsayılan eşlemeyi engelleyin.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Profil hizmeti

Sunucu uygulamasında bir `ProfileService` uygulama oluşturun.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

Sunucu uygulamasında profil hizmetini şu şekilde kaydedin `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Yetkilendirme mekanizmalarını kullanma

Istemci uygulamasında, bileşen yetkilendirme yaklaşımları bu noktada işlevseldir. Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, kullanıcıyı yetkilendirmek için bir rol kullanabilir:

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

`User.Identity.Name`, genellikle oturum açma e-posta adresi olan kullanıcının kullanıcı adıyla Istemci uygulamasına doldurulur.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure App Service dağıtım](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault bir sertifikayı içeri aktarma (Azure belgeleri)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
