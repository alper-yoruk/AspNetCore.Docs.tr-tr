---
title: Identity Server Blazor ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale getirin
author: guardrex
description: Visual Studio Blazor içinden kimlik doğrulaması ile [identityServer](https://identityserver.io/) arka uç kullanan yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791612"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Identity Server Blazor ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale getirin

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Visual Studio'da kullanıcıların ve API aramalarını doğrulamak için Blazor [IdentityServer'ı](https://identityserver.io/) kullanan yeni bir barındırılan uygulama oluşturmak için:

1. Yeni bir ** Blazor WebAssembly** uygulaması oluşturmak için Visual Studio'yı kullanın. Daha fazla bilgi için bkz. <xref:blazor/get-started>.
1. ** Blazor Yeni bir uygulama oluştur** iletişim kutusunda Kimlik **Doğrulama** bölümünde **Değiştir'i** seçin.
1. Ok'un ardından **Bireysel Kullanıcı Hesapları'nı** **seçin.**
1. **Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.
1. **Oluştur** düğmesini seçin.

Uygulamayı komut kabuğunda oluşturmak için aşağıdaki komutu uygulayın:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ). Klasör adı da projenin adının bir parçası olur.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

Aşağıdaki bölümlerde kimlik doğrulama desteği eklendiğinde projeye yapılan eklemeler açıklanır.

### <a name="startup-class"></a>Başlangıç sınıfı

Sınıfın `Startup` aşağıdaki eklemeleri vardır:

* `Startup.ConfigureServices` içinde:

  * Kimlik:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer'ın <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> üstüne bazı varsayılan ASP.NET Core kuralları nı ayarlayan ek bir yardımcı yöntemiyle IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> üretilen JWT belirteçlerini doğrulamak için uygulamayı yapılandıran ek bir yardımcı yöntemiyle kimlik doğrulama:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` içinde:

  * İstek kimlik bilgilerini doğrulamave kullanıcıyı istek bağlamına ayarlamadan sorumlu kimlik doğrulama aracı:

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect (OIDC) uç noktalarını ortaya çıkaran IdentityServer ara yazılımı:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Yardımcı [yöntemi, IdentityServer'ı](https://identityserver.io/) ASP.NET Core senaryoları için yapılandırır. IdentityServer, uygulama güvenliği yle ilgili endişeleri işlemek için güçlü ve genişletilebilir bir çerçevedir. IdentityServer en yaygın senaryolar için gereksiz karmaşıklığı ortaya çıkarır. Sonuç olarak, iyi bir başlangıç noktası olarak düşünmemiz koşuluyla bir dizi konvansiyon ve yapılandırma seçeneği sağlanır. Kimlik doğrulama gereksinimleriniz değiştiğinde, IdentityServer'ın tüm gücü, bir uygulamanın gereksinimlerine uyacak şekilde kimlik doğrulamayı özelleştirmeye devam eder.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Yardımcı yöntemi varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, Identity'in Kimlik URL alanındaki `/Identity`herhangi bir alt yola yönlendirilen tüm istekleri işlemesine izin verecek şekilde yapılandırılmıştır. Diğer <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> tüm istekleri işler. Ayrıca, bu yöntem:

* Varsayılan kapsamı `{APPLICATION NAME}API` .'ye sahip bir API `{APPLICATION NAME}API`kaynağını IdentityServer'a kaydeder.
* Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak için JWT Taşıyıcı Belirteç Orta ware'i yapılandırır.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

*(Controllers/WeatherForecastController.cs)* olarak [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sınıfa öznitelik uygulanır. `WeatherForecastController` Öznitelik, kaynağa erişmek için varsayılan ilkeyi temel alan kullanıcıya yetki verilmesi gerektiğini belirtir. Varsayılan yetkilendirme ilkesi, daha önce bahsedilen ilke düzeni <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır. Yardımcı yöntemi, uygulamaya <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gelen istekler için varsayılan işleyici olarak yapılandırır.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` (Data/ApplicationDbContext.cs)*(Data/ApplicationDbContext.cs) (Data/ApplicationDbContext.cs)* olarak, IdentityServer'ın şemasını içerecek şekilde genişletmek <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> dışında kimlikte de kullanılır. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601><xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>türetilmiştir.

Veritabanı şemasının tam denetimini elde etmek için, <xref:Microsoft.EntityFrameworkCore.DbContext> kullanılabilir Kimlik sınıflarından birinden devralır ve `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` yönteme çağırarak bağlamı Kimlik şemasını içerecek şekilde yapılandırın.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* olarak, istemci uç noktası OIDC parametrelerini sunmak üzere sağlanır.

### <a name="app-settings-files"></a>Uygulama ayarları dosyaları

Proje kökündeki uygulama ayarları dosyasında *(appsettings.json)* `IdentityServer` bölümünde, yapılandırılan istemcilerin listesi açıklanır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı uygulama adına karşılık gelir ve OAuth `ClientId` parametresi için sözleşme ile eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Profil, sunucunun yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Geliştirme ortamı uygulama ayarları dosyasında (*uygulama ayarları. Development.json*) proje kökünde, `IdentityServer` belirteçleri imzalamak için kullanılan anahtarı açıklar. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>İstemci uygulaması yapılandırması

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Bireysel Kullanıcı Hesapları'nı kullanmak için`Individual`bir uygulama oluşturulduğunda , uygulama `Microsoft.AspNetCore.Components.WebAssembly.Authentication` otomatik olarak uygulamanın proje dosyasındaki paket için bir paket referansı alır. Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.

Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.

### <a name="api-authorization-support"></a>API yetkilendirme desteği

Kullanıcıların kimlik doğrulaması desteği, paket içinde sağlanan uzantı yöntemi `Microsoft.AspNetCore.Components.WebAssembly.Authentication` yle servis kabına takılır. Bu yöntem, uygulamanın varolan yetkilendirme sistemiyle etkileşim kurabilmesi için gereken tüm hizmetleri ayarlar.

```csharp
builder.Services.AddApiAuthorization();
```

Varsayılan olarak, uygulama nın yapılandırmasını kurala göre `_configuration/{client-id}`yükler. Sözleşmeye göre, istemci kimliği uygulamanın montaj adına ayarlanır. Bu URL, seçeneklerle aşırı yüklemeyi çağırarak ayrı bir bitiş noktasına işaret etmek üzere değiştirilebilir.

### <a name="imports-file"></a>Dosyayı alma

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

Bileşen `LoginDisplay` *(Paylaşılan/LoginDisplay.razor)* `MainLayout` bileşeni *(Shared/MainLayout.razor)* işlenir ve aşağıdaki davranışları yönetir:

* Kimliği doğrulanmış kullanıcılar için:
  * Geçerli kullanıcı adını görüntüler.
  * ASP.NET Çekirdek Kimlik'teki kullanıcı profili sayfasına bir bağlantı sunar.
  * Uygulamadan çıkmak için bir düğme sunar.
* Anonim kullanıcılar için:
  * Kayıt seçeneği sunar.
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

Uygulamayı Sunucu projesinden çalıştırın. Visual Studio'yu kullanırken, **Solution Explorer'daki** Sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya **Hata Ayıklama** menüsünden uygulamayı başlatın.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Ek erişim belirteçleri isteme](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
