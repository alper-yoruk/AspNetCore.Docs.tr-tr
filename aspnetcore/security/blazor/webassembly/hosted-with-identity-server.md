---
title: Kimlik sunucusuyla ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: Bir [IdentityServer](https://identityserver.io/) arka Blazor ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110958"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Kimlik sunucusuyla ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir. Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.

Visual Studio 'da, Blazor KULLANıCıLARıN ve API çağrılarının kimliğini doğrulamak Için [IdentityServer](https://identityserver.io/) kullanan yeni bir barındırılan uygulama oluşturmak için:

1. Yeni ** Blazor bir webassembly** uygulaması oluşturmak için Visual Studio 'yu kullanın. Daha fazla bilgi için bkz. <xref:blazor/get-started>.
1. ** Blazor Yeni uygulama oluştur** iletişim kutusunda **kimlik doğrulama** bölümünde **Değiştir** ' i seçin.
1. **Her kullanıcı hesabını** ve ardından **Tamam ' ı**seçin.
1. **Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.
1. **Oluştur** düğmesini seçin.

Uygulamayı bir komut kabuğunda oluşturmak için aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`). Klasör adı Ayrıca projenin adının bir parçası haline gelir.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.

### <a name="startup-class"></a>Başlangıç sınıfı

`Startup` Sınıfı aşağıdaki eklemelere sahiptir:

* `Startup.ConfigureServices` içinde:

  * IDENTITY

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer 'ın en üstünde <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Kimliği, IdentityServer <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> tarafından üretilen JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:

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

### <a name="addapiauthorization"></a>Addadpiauthorization

Yardımcı <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> yöntemi, [ıdentityserver](https://identityserver.io/) 'ı ASP.NET Core senaryolar için yapılandırır. IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir. IdentityServer, en yaygın senaryolar için gereksiz karmaşıklık sunar. Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır. Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için hala kullanılabilir.

### <a name="addidentityserverjwt"></a>Addentityserverjwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, kimliğin kimlik URL 'sindeki herhangi bir alt yolda yönlendirilen tüm istekleri işlemesine izin verecek şekilde yapılandırılmıştır `/Identity`. Diğer <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> tüm istekleri işler. Ayrıca, bu yöntem:

* IdentityServer `{APPLICATION NAME}API` ile bir API kaynağını varsayılan kapsamına kaydeder `{APPLICATION NAME}API`.
* Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.

### <a name="weatherforecastcontroller"></a>Dalgalı bir denetleyici

`WeatherForecastController` (*Controllers/dalgalı therforeroı Controller. cs*) içinde, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır. Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir. Varsayılan yetkilendirme ilkesi, tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> daha önce bahsedilen ilke şemasına ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır. Yardımcı yöntemi, uygulamaya <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> yönelik istekler için varsayılan işleyici olarak yapılandırır.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` (*Data/applicationdbcontext. cs*) öğesinde aynı <xref:Microsoft.EntityFrameworkCore.DbContext> şekilde, kimlik içinde, IdentityServer şemasını dahil etmek için genişlettiği <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> özel durumla birlikte kullanılır. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>türetilir.

Veritabanı şeması üzerinde tam denetim elde etmek için, kullanılabilir kimlik <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflarından birini ve `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` yöntemi metodunu çağırarak kimlik şemasını içerecek şekilde yapılandırın.

### <a name="oidcconfigurationcontroller"></a>Oıdcconfigurationcontroller

`OidcConfigurationController` (*Controllers/oıdcconfigurationcontroller. cs*) öğesinde, istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.

### <a name="app-settings-files"></a>Uygulama ayarları dosyaları

Proje kökündeki App settings dosyasında (*appSettings. JSON*), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Geliştirme ortamı uygulama ayarları dosyasında (*appSettings. Development. JSON*) proje kökünde, `IdentityServer` bölüm belirteçleri imzalamak için kullanılan anahtarı açıklar. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>İstemci uygulama yapılandırması

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Bireysel kullanıcı hesapları (`Individual`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak uygulamanın proje dosyasındaki `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket için bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.

### <a name="api-authorization-support"></a>API yetkilendirme desteği

Kullanıcıları kimlik doğrulama desteği, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır. Bu yöntem, uygulamanın var olan yetkilendirme sistemiyle etkileşime geçmesini sağlamak için gereken tüm hizmetleri ayarlar.

```csharp
builder.Services.AddApiAuthorization();
```

Varsayılan olarak, uygulamayı kuralına göre uygulamanın yapılandırmasını yükler `_configuration/{client-id}`. Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır. Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.

### <a name="imports-file"></a>Dosya içeri aktarmalar

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

Bileşen (*paylaşılan/LoginDisplay.* Razor) `MainLayout` , bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir: `LoginDisplay`

* Kimliği doğrulanmış kullanıcılar için:
  * Geçerli Kullanıcı adını görüntüler.
  * ASP.NET Core kimliği içindeki kullanıcı profili sayfasına bir bağlantı sunar.
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
