---
title: BlazorSunucu ile ASP.NET Core weelsembly barındırılan uygulamasının güvenliğini Identity sağlama
author: guardrex
description: BlazorBir [IdentityServer](https://identityserver.io/) arka ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153629"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>BlazorSunucu ile ASP.NET Core weelsembly barındırılan uygulamasının güvenliğini Identity sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorVisual Studio 'da, kullanıcıların ve API çağrılarının kimliğini doğrulamak Için [IdentityServer](https://identityserver.io/) kullanan yeni bir barındırılan uygulama oluşturmak için:

1. Yeni bir ** Blazor webassembly** uygulaması oluşturmak Için Visual Studio 'yu kullanın. Daha fazla bilgi için bkz. <xref:blazor/get-started>.
1. **Yeni Blazor uygulama oluştur** Iletişim kutusunda **kimlik doğrulama** bölümünde **Değiştir** ' i seçin.
1. **Her kullanıcı hesabını** ve ardından **Tamam ' ı**seçin.
1. **Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.
1. **Oluştur** düğmesini seçin.

Uygulamayı bir komut kabuğunda oluşturmak için aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ). Klasör adı Ayrıca projenin adının bir parçası haline gelir.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.

### <a name="startup-class"></a>Başlangıç sınıfı

`Startup`Sınıfı aşağıdaki eklemelere sahiptir:

* `Startup.ConfigureServices` içinde:

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 'ın en üstünde bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Kimliği <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> , IdentityServer tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` içinde:

  * İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:

    ```csharp
    app.UseAuthentication();
    ```

  * Açık KIMLIK Connect (OıDC) uç noktalarını kullanıma sunan IdentityServer ara yazılımı:

    ```csharp
    app.UseIdentityServer();
    ```

  * Kimlik doğrulama ve yetkilendirme ara yazılımı:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>Addadpiauthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Yardımcı yöntemi, [ıdentityserver](https://identityserver.io/) 'ı ASP.NET Core senaryolar için yapılandırır. IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir. IdentityServer, en yaygın senaryolar için gereksiz karmaşıklık sunar. Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır. Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için hala kullanılabilir.

### <a name="addidentityserverjwt"></a>Addentityserverjwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, Identity URL alanındaki herhangi bir alt yolda yönlendirilen tüm istekleri işlemeye izin verecek şekilde yapılandırılmıştır Identity `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Diğer tüm istekleri işler. Ayrıca, bu yöntem:

* `{APPLICATION NAME}API`IdentityServer ile bir API kaynağını varsayılan kapsamına kaydeder `{APPLICATION NAME}API` .
* Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.

### <a name="weatherforecastcontroller"></a>Dalgalı bir denetleyici

`WeatherForecastController`(*Controllers/dalgalı Therforeroı Controller. cs*) içinde, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır. Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir. Varsayılan yetkilendirme ilkesi, tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> daha önce bahsedilen ilke şemasına ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır. Yardımcı yöntemi, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> uygulamaya yönelik istekler için varsayılan işleyici olarak yapılandırır.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`(*Data/ApplicationDbContext. cs*) öğesinde, aynı şekilde, <xref:Microsoft.EntityFrameworkCore.DbContext> Identity <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> IdentityServer şemasını dahil etmek için genişlettiği özel durumla birlikte kullanılır. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflardan birini ve Identity yöntemi metodunu çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .

### <a name="oidcconfigurationcontroller"></a>Oıdcconfigurationcontroller

`OidcConfigurationController`(*Controllers/Oıdcconfigurationcontroller. cs*) öğesinde, istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.

### <a name="app-settings-files"></a>Uygulama ayarları dosyaları

Proje kökündeki App settings dosyasında (*appSettings. JSON*), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>İstemci uygulama yapılandırması

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak `Microsoft.AspNetCore.Components.WebAssembly.Authentication` uygulamanın proje dosyasındaki paket için bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

`{VERSION}`Yukarıdaki paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` makalede gösterilen paketin sürümüyle değiştirin <xref:blazor/get-started> .

### <a name="api-authorization-support"></a>API yetkilendirme desteği

Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır `Microsoft.AspNetCore.Components.WebAssembly.Authentication` . Bu yöntem, uygulamanın var olan yetkilendirme sistemiyle etkileşime geçmesini sağlamak için gereken tüm hizmetleri ayarlar.

```csharp
builder.Services.AddApiAuthorization();
```

Varsayılan olarak, uygulamayı kuralına göre uygulamanın yapılandırmasını yükler `_configuration/{client-id}` . Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır. Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.

### <a name="imports-file"></a>Dosya içeri aktarmalar

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

`LoginDisplay`Bileşen (*paylaşılan/logindisplay.* Razor), `MainLayout` bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir:

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

Uygulamayı sunucu projesinden çalıştırın. Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
