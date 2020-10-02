---
title: Barındırılan bir ASP.NET Core Blazor WebAssembly uygulamasının sunucuyla güvenliğini Identity sağlama
author: guardrex
description: Barındırılan bir ASP.NET Core uygulamasının sunucusuyla güvenliğini nasıl sağlayacağınızı öğrenin Blazor WebAssembly Identity .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0d63ddbc730d3feef0682f6e49dd1b1b4d5e0301
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636822"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Blazor WebAssemblySunucu ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama Identity

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Bu makalede, kullanıcıların ve API çağrılarının kimliğini doğrulamak için [ Identity sunucusunu](https://identityserver.io/) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanmaktadır.

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

  * ASP.NET Core Identity:

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

`ApplicationDbContext`() İçinde `Data/ApplicationDbContext.cs` , <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> sunucu için şemayı içerecek şekilde genişletilir Identity . <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> , öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

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

Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)adresindeki **sürüm geçmişinde** bulunabilir.

### <a name="httpclient-configuration"></a>`HttpClient` yapılandırmada

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

Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) . Bu yöntem, uygulamanın mevcut yetkilendirme sistemiyle etkileşimde bulunmak için gereken hizmetleri ayarlar.

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
  * İçindeki kullanıcı profili sayfasına bir bağlantı sunar ASP.NET Core Identity .
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

Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun. Identity Sunucu, tek bir talep içinde birden çok rolü JSON dizisi olarak gönderir `role` . Tek bir rol, talepte bir dize değeri olarak gönderilir. Fabrika, `role` kullanıcının rollerinin her biri için ayrı bir talep oluşturur.

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

`User.Identity.Name` , genellikle oturum açma e-posta adresi olan kullanıcının kullanıcı adıyla Istemci uygulamasına doldurulur.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Özel bir etki alanı ile Azure App Service barındırın

Aşağıdaki kılavuzda, Blazor WebAssembly Identity özel bir etki alanı ile [Azure App Service](https://azure.microsoft.com/services/app-service/) üzere barındırılan bir uygulamanın sunucu ile nasıl dağıtılacağı açıklanmaktadır.

Bu barındırma senaryosunda, [ Identity sunucunun belirteç imzalama anahtarı](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) için aynı SERTIFIKAYı ve sitenin https güvenli iletişimini tarayıcılarla **birlikte kullanmayın:**

* Bu iki gereksinim için farklı sertifikaların kullanılması, her amaçla özel anahtarları yalıtdığından iyi bir güvenlik uygulamasıdır.
* Tarayıcılarla iletişim için TLS sertifikaları, sunucunun belirteç imzasını etkilemeden bağımsız olarak yönetilir Identity .
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , özel etki alanı bağlamaya yönelik bir App Service uygulamasına sertifika sağladığı zaman, Identity sunucu, belirteç imzalama için Azure Key Vault aynı sertifikayı elde edemiyor. IdentitySunucuyu fiziksel bir yoldan aynı TLS sertifikasını kullanacak şekilde yapılandırmak mümkün olsa da, güvenlik sertifikalarının kaynak denetimine yerleştirilmesi **kötü bir uygulamadır ve çoğu senaryoda kaçınılmalıdır**.

Aşağıdaki kılavuzda, yalnızca sunucu belirteci imzalama için Azure Key Vault ' de otomatik olarak imzalanan bir sertifika oluşturulur Identity . IdentitySunucu yapılandırması, uygulamanın `My`  >  sertifika deposu aracılığıyla Anahtar Kasası sertifikasını kullanır `CurrentUser` . Özel etki alanları olan HTTPS trafiği için kullanılan diğer sertifikalar, sunucu imzalama sertifikasından ayrı olarak oluşturulur ve yapılandırılır Identity .

Bir uygulamayı, Azure App Service ve Azure Key Vault özel bir etki alanı ve HTTPS ile barındıracak şekilde yapılandırmak için:

1. Plan düzeyi veya daha yüksek bir [App Service planı](/azure/app-service/overview-hosting-plans) oluşturun `Basic B1` . App Service, bir `Basic B1` veya daha yüksek hizmet katmanının özel etki alanlarını kullanmasını gerektirir.
1. Sitenin güvenli tarayıcı iletişimi (HTTPS protokolü) için, kuruluşunuzun denetlediği sitenin tam etki alanı adı (FQDN) olan bir PFX sertifikası oluşturun (örneğin, `www.contoso.com` ). Sertifikayı şu şekilde oluşturun:
   * Anahtar kullanımları
     * Dijital imza doğrulaması ( `digitalSignature` )
     * Anahtar şifreleme ( `keyEncipherment` )
   * Gelişmiş/genişletilmiş anahtar kullanımları
     * İstemci kimlik doğrulaması (1.3.6.1.5.5.7.3.2)
     * Sunucu kimlik doğrulaması (1.3.6.1.5.5.7.3.1)

   Sertifikayı oluşturmak için aşağıdaki yaklaşımlardan birini veya başka bir uygun aracı ya da çevrimiçi hizmeti kullanın:

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [Windows üzerinde MakeCert](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Daha sonra sertifikayı Azure Key Vault aktarmak için kullanılan parolayı unutmayın.

   Azure Key Vault sertifikaları hakkında daha fazla bilgi için bkz. [Azure Key Vault: sertifikalar](/azure/key-vault/certificates/).
1. Yeni bir Azure Key Vault oluşturun veya Azure aboneliğinizde mevcut bir anahtar kasasını kullanın.
1. Anahtar kasasının **Sertifikalar** alanında PFX site sertifikasını içeri aktarın. Uygulamanın yapılandırmasında daha sonra kullanılan sertifikanın parmak izini kaydedin.
1. Azure Key Vault, sunucu belirteci imzalama için otomatik olarak imzalanan yeni bir sertifika oluşturun Identity . Sertifikaya bir **sertifika adı** ve **Konu**verin. **Konusu** , `CN={COMMON NAME}` `{COMMON NAME}` yer tutucunun sertifikanın ortak adı olduğu gibi belirtilir. Ortak ad herhangi bir alfasayısal dize olabilir. Örneğin, `CN=IdentityServerSigning` geçerli bir sertifika **konusudur**. Varsayılan **Gelişmiş Ilke yapılandırma** ayarlarını kullanın. Uygulamanın yapılandırmasında daha sonra kullanılan sertifikanın parmak izini kaydedin.
1. Azure portal Azure App Service gidin ve aşağıdaki yapılandırmayla yeni bir App Service oluşturun:
   * **Publish** Kümeyi olarak Yayımla `Code` .
   * **Çalışma zamanı yığını** , uygulamanın çalışma zamanına ayarlandı.
   * **SKU ve boyut**için App Service katmanının `Basic B1` veya daha yüksek olduğunu doğrulayın.  App Service, bir `Basic B1` veya daha yüksek hizmet katmanının özel etki alanlarını kullanmasını gerektirir.
1. Azure App Service oluşturduktan sonra, uygulamanın **yapılandırmasını** açın ve daha önce kaydedilen sertifika parmak izlerini belirten yeni bir uygulama ayarı ekleyin. Uygulama ayarı anahtarı `WEBSITE_LOAD_CERTIFICATES` . Aşağıdaki örnekte gösterildiği gibi, uygulama ayarı değerindeki sertifika parmak izlerini bir virgülle ayırın:
   * Anahtar: `WEBSITE_LOAD_CERTIFICATES`
   * Değer: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   Azure portal, uygulama ayarlarının kaydedilmesi iki adımlı bir işlemdir: `WEBSITE_LOAD_CERTIFICATES` anahtar-değer ayarını kaydedin, ardından dikey pencerenin en üstündeki **Kaydet** düğmesini seçin.
1. Uygulamanın **TLS/SSL ayarlarını**seçin. **Özel anahtar sertifikaları (. pfx)** seçeneğini belirleyin. HTTPS iletişimi için sitenin sertifikasını ve sitenin otomatik olarak imzalanan sunucu belirteci imzalama sertifikasını içeri aktarmak için **Key Vault sertifikayı Içeri aktar** işlemini iki kez kullanın Identity .
1. **Özel etki alanları** dikey penceresine gidin. Etki alanı Kaydedicinizin Web sitesinde, etki alanını yapılandırmak için **IP adresini** ve **özel etkı alanı doğrulama kimliğini** kullanın. Tipik bir etki alanı yapılandırması şunları içerir:
   * Azure portal **ana bilgisayarı** ve IP adresi değeri olan **bir kayıt** `@` .
   * **Ana bilgisayarı** **TXT Record** `asuid` ve Azure tarafından oluşturulan ve Azure Portal tarafından oluşturulan doğrulama kimliği değeri olan bir TXT kaydı.

   Değişiklikleri etki alanı Kaydedicinizin Web sitesinde doğru bir şekilde kaydettiğinizden emin olun. Bazı kaydedici Web siteleri, etki alanı kayıtlarını kaydetmek için iki adımlı bir işlem gerektirir: bir veya daha fazla kayıt, bir veya daha fazla kaydın ayrı bir düğmeyle güncelleştirilmesi tarafından tek tek kaydedilir.
1. Azure portal **özel etki alanları** dikey penceresine geri dönün. **Özel etki alanı ekle**'yi seçin. **Bir kayıt** seçeneğini belirleyin. Etki alanını girip **Doğrula**' yı seçin. Etki alanı kayıtları doğruysa ve Internet üzerinden yayıldığında, portal **özel etki alanı Ekle** düğmesini seçmenizi sağlar.

   Etki alanı kayıt şirketiniz tarafından işlendikten sonra, etki alanı kaydı değişikliklerinin Internet etki alanı ad sunucuları (DNS) üzerinden yayılması birkaç gün sürebilir. Etki alanı kayıtları üç iş günü içinde güncellenmemişse, kayıtların etki alanı kaydedicisinde doğru şekilde ayarlandığını onaylayın ve müşteri desteğiyle iletişim kurun.
1. **Özel etki alanları** dikey penceresinde, etki alanı IÇIN **SSL durumu** işaretlenir `Not Secure` . **Bağlama Ekle** bağlantısını seçin. Özel etki alanı bağlamasının anahtar kasasından site HTTPS sertifikasını seçin.
1. Visual Studio 'da, *sunucu* projesinin uygulama ayarları dosyasını ( `appsettings.json` veya `appsettings.Production.json` ) açın. IdentitySunucu yapılandırması ' nda aşağıdaki `Key` bölümü ekleyin. Anahtar için otomatik olarak imzalanan sertifika **konusunu** belirtin `Name` . Aşağıdaki örnekte, sertifikanın anahtar kasasında atanan ortak adı ' dır; `IdentityServerSigning` Bu da bir **konuyu** veren `CN=IdentityServerSigning` :

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. Visual Studio 'da *sunucu* projesi için bir Azure App Service [Yayımlama profili](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) oluşturun. Menü çubuğundan: **derleme**  >  **Publish**  >  **Yeni**  >  **Azure**  >  **Azure App Service** yayımlama (Windows veya Linux) öğesini seçin. Visual Studio bir Azure aboneliğine bağlıyken, Azure kaynaklarının **görünümünü** **kaynak türüne**göre ayarlayabilirsiniz. Uygulamanın App Service bulmak ve bunu seçmek için **Web uygulaması** listesinde gezinin. **Son**’u seçin.
1. Visual Studio **Yayımla** penceresine döndüğünde, anahtar kasası ve SQL Server veritabanı hizmeti bağımlılıkları otomatik olarak algılanır.

   Anahtar Kasası hizmeti için varsayılan ayarlarda yapılandırma değişikliği yapılması gerekmez.

   Test amacıyla, bir uygulamanın varsayılan olarak şablon tarafından yapılandırılan yerel [SQLite](https://www.sqlite.org/index.html) veritabanı, Blazor ek yapılandırma olmadan uygulamayla birlikte dağıtılabilir. Üretimde sunucu için farklı bir veritabanını yapılandırmak, Identity Bu makalenin kapsamı dışındadır. Daha fazla bilgi için aşağıdaki belge kümelerinde veritabanı kaynaklarına bakın:
   * [App Service](/azure/app-service/)
   * [Identity Server](https://identityserver4.readthedocs.io/en/latest/)

1. Pencerenin üst kısmındaki dağıtım profili adı altındaki **Düzenle** bağlantısını seçin. Hedef URL 'yi sitenin özel etki alanı URL 'SI olarak değiştirin (örneğin, `https://www.contoso.com` ). Ayarları kaydedin.
1. Uygulamayı yayımlayın. Visual Studio bir tarayıcı penceresi açar ve siteyi kendi özel etki alanında ister.

Azure belgeleri, Azure hizmetleri ve özel etki alanlarını, bir kayıt yerine CNAME kayıtlarını kullanma hakkında bilgi dahil olmak üzere App Service 'de TLS bağlama ile kullanmayla ilgili ek bilgiler içerir. Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [App Service belgeleri](/azure/app-service/)
* [Öğretici: mevcut bir özel DNS adını Azure App Service eşleme](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Uygulama, uygulama yapılandırması veya Azure portal Azure hizmetlerinde yapılan bir değişiklikten sonra her uygulama test çalışması için yeni bir özel veya geçersiz tarayıcı penceresi kullanmanızı öneririz. cookieÖnceki bir test çalıştırağından kalan öğeleri, site yapılandırması doğru olsa bile siteyi test ederken başarısız kimlik doğrulama veya yetkilendirmeyle sonuçlanabilir. Visual Studio 'Yu her test çalışması için yeni bir özel veya bir tarayıcı penceresi açmak üzere yapılandırma hakkında daha fazla bilgi için, [ Cookie s ve site verileri](#cookies-and-site-data) bölümüne bakın.

Azure portal App Service yapılandırma değiştirildiğinde, güncelleştirmeler genellikle hızla etkili olur ancak anında gerçekleşir. Bazen, bir yapılandırma değişikliğinin etkili olabilmesi için bir App Service yeniden başlatılması için kısa bir süre beklemeniz gerekir.

Bir sertifika yükleme sorununu giderirken, Azure portal [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell komut kabuğu 'nda aşağıdaki komutu yürütün. Komut, uygulamanın sertifika deposundan erişebileceği sertifikaların bir listesini sağlar `My`  >  `CurrentUser` . Çıktıda sertifika konuları ve parmak izleri, bir uygulamada hata ayıklanırken yararlı olur:

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure App Service dağıtım](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault bir sertifikayı içeri aktarma (Azure belgeleri)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
