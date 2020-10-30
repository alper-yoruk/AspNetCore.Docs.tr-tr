---
title: Kimlik doğrulamasını Identity ASP.NET Core ve 2,0 'e geçirin
author: scottaddie
description: Bu makalede ASP.NET Core 1. x kimlik doğrulamasını ve ASP.NET Core 2,0 ' ye geçirmek için en yaygın adımlar özetlenmektedir Identity .
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057186"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a>Kimlik doğrulamasını Identity ASP.NET Core ve 2,0 'e geçirin

[Scott Ade](https://github.com/scottaddie) ve [Hao Kung](https://github.com/HaoK) tarafından

ASP.NET Core 2,0, kimlik doğrulaması için yeni bir modele sahiptir ve [Identity](xref:security/authentication/identity) Hizmetleri kullanarak yapılandırmayı basitleştirir. Kimlik doğrulaması kullanan veya Identity yeni modeli aşağıda özetlenen şekilde kullanacak şekilde güncelleştirilebilen 1. x uygulama ASP.NET Core.

## <a name="update-namespaces"></a>Ad alanlarını güncelleştirme

1. x içinde `IdentityRole` ve `IdentityUser` ad alanında bulunan sınıflar `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

2,0 ' de, <xref:Microsoft.AspNetCore.Identity> ad alanı bu tür sınıfların birkaçı için yeni giriş haline geldi. Varsayılan Identity kodla, etkilenen sınıflar `ApplicationUser` ve içerir `Startup` . `using`Etkilenen başvuruları çözümlemek için deyimlerinizi ayarlayın.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Kimlik doğrulama ara yazılımı ve Hizmetleri

1. x projelerinde kimlik doğrulaması, ara yazılım aracılığıyla yapılandırılır. Desteklemek istediğiniz her kimlik doğrulama düzeni için bir ara yazılım yöntemi çağırılır.

Aşağıdaki 1. x örneği, Startup.cs içinde Facebook kimlik doğrulamasını yapılandırır Identity : *Startup.cs*

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

2,0 projesinde, kimlik doğrulaması hizmetler aracılığıyla yapılandırılır. Her kimlik doğrulama şeması, `ConfigureServices` *Startup.cs* yöntemine kaydedilir. `UseIdentity`Yöntemi ile değiştirilmiştir `UseAuthentication` .

Aşağıdaki 2,0 örneği, Startup.cs içinde Facebook kimlik doğrulamasını yapılandırır Identity : *Startup.cs*

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication`Yöntemi otomatik kimlik doğrulamasından ve uzak kimlik doğrulama isteklerinin işlenmesinden sorumlu olan tek bir kimlik doğrulama ara yazılım bileşeni ekler. Tek, ortak bir ara yazılım bileşeniyle tüm bireysel ara yazılım bileşenlerini değiştirir.

Her bir büyük kimlik doğrulama şeması için 2,0 geçiş yönergeleri aşağıda verilmiştir.

### <a name="no-loccookie-based-authentication"></a>Cookietabanlı kimlik doğrulaması

Aşağıdaki iki seçenekten birini seçin ve *Startup.cs* 'de gerekli değişiklikleri yapın:

1. cookieİle s kullanınIdentity
    - `UseIdentity`Yönteminde ile değiştirin `UseAuthentication` `Configure` :

        ```csharp
        app.UseAuthentication();
        ```

    - `AddIdentity` `ConfigureServices` Kimlik doğrulama hizmetlerini eklemek için yöntemindeki yöntemi çağırın cookie .
    - İsteğe bağlı olarak, `ConfigureApplicationCookie` `ConfigureExternalCookie` `ConfigureServices` ayarları ince ayar için yöntemi içindeki or metodunu çağırın Identity cookie .

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Şunu cookie olmadan s kullan Identity
    - `UseCookieAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - `AddAuthentication` `AddCookie` Yöntemi içindeki ve yöntemlerini çağırın `ConfigureServices` :

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT taşıyıcı kimlik doğrulaması

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:
- `UseJwtBearerAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddJwtBearer` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Bu kod parçacığı kullanmaz Identity , bu nedenle varsayılan şema yöntemine geçirerek ayarlanmalıdır `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` .

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OıDC) kimlik doğrulaması

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:

- `UseOpenIdConnectAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddOpenIdConnect` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- `PostLogoutRedirectUri` `OpenIdConnectOptions` Eylemdeki özelliğini ile değiştirin `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook kimlik doğrulaması

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:
- `UseFacebookAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddFacebook` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google kimlik doğrulaması

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:
- `UseGoogleAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddGoogle` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft Hesabı kimlik doğrulaması

Microsoft hesabı kimlik doğrulaması hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/14455)bakın.

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:
- `UseMicrosoftAccountAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddMicrosoftAccount` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter kimlik doğrulaması

*Startup.cs* içinde aşağıdaki değişiklikleri yapın:
- `UseTwitterAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Yönteminde `AddTwitter` yöntemi çağırın `ConfigureServices` :

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Varsayılan kimlik doğrulama düzenlerini ayarlama

1. x içinde, `AutomaticAuthenticate` `AutomaticChallenge` [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) temel sınıfının ve özellikleri tek bir kimlik doğrulama düzeninde ayarlamaya yöneliktir. Bunu zorlayacağından iyi bir yol yoktu.

2,0 ' de, bu iki özellik ayrı bir örnekteki özellikler olarak kaldırılmıştır `AuthenticationOptions` . `AddAuthentication`Startup.cs yöntemi içinde yöntem çağrısında yapılandırılabilirler `ConfigureServices` : *Startup.cs*

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Yukarıdaki kod parçacığında, varsayılan düzen `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s") olarak ayarlanır.

Alternatif olarak, birden `AddAuthentication` fazla özellik ayarlamak için yönteminin aşırı yüklenmiş bir sürümünü kullanın. Aşağıdaki aşırı yüklenmiş yöntem örneğinde, varsayılan düzen olarak ayarlanır `CookieAuthenticationDefaults.AuthenticationScheme` . Kimlik doğrulama düzeni, farklı `[Authorize]` özniteliklerde veya yetkilendirme ilkeleriniz içinde de belirtilebilir.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Aşağıdaki koşullardan biri doğru ise, 2,0 'de varsayılan bir düzen tanımlayın:
- Kullanıcının otomatik olarak oturum açabilmesi istiyorsunuz
- `[Authorize]`Şemaları belirtmeden özniteliği veya yetkilendirme ilkelerini kullanın

Bu kural için bir özel durum `AddIdentity` yöntemidir. Bu yöntem cookie sizin için ekler ve varsayılan kimlik doğrulama ve zorluk düzenlerini uygulamaya ayarlar cookie `IdentityConstants.ApplicationScheme` . Ayrıca, varsayılan oturum açma düzenini dış olarak ayarlar cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext kimlik doğrulama uzantılarını kullanma

`IAuthenticationManager`Arabirim, 1. x kimlik doğrulama sistemine ana giriş noktasıdır. `HttpContext`Ad alanındaki yeni bir genişletme yöntemleri kümesiyle değiştirilmiştir `Microsoft.AspNetCore.Authentication` .

Örneğin, 1. x projeleri bir özelliğe başvurur `Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2,0 projesinde, `Microsoft.AspNetCore.Authentication` ad alanını içeri aktarın ve `Authentication` özellik başvurularını silin:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows kimlik doğrulaması (HTTP.sys/Iisıntegration)

Windows kimlik doğrulamasının iki farklı çeşidi vardır:

* Konak yalnızca kimliği doğrulanmış kullanıcılara izin verir. Bu çeşitleme 2,0 değişikliklerden etkilenmez.
* Konak hem anonim hem de kimliği doğrulanmış kullanıcılara izin verir. Bu çeşitleme 2,0 değişikliklerden etkilenir. Örneğin, uygulama [IIS](xref:host-and-deploy/iis/index) veya [HTTP.sys](xref:fundamentals/servers/httpsys) katmanında anonim kullanıcılara izin vermeli, ancak kullanıcıları denetleyici düzeyinde yetkilendirebilmelidir. Bu senaryoda, yönteminde varsayılan düzeni ayarlayın `Startup.ConfigureServices` .

  [Microsoft. AspNetCore. Server. ıısıntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)için varsayılan düzeni şu şekilde ayarlayın `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)için varsayılan düzeni olarak ayarlayın `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Varsayılan düzenin ayarlanamaması, yetkilendirme (sınama) isteğinin şu özel durumla çalışmasını engeller:

  > `System.InvalidOperationException`: Hiçbir authenticationScheme belirtilmedi ve hiç Defaultchallenbescheme bulunamadı.

Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a>IdentityCookieSeçenek örnekleri

2,0 değişikliğin yan etkisi, seçenek örnekleri yerine adlandırılmış seçenekleri kullanma anahtarıdır cookie . Identity cookie Düzen adlarını özelleştirme özelliği kaldırılır.

Örneğin, 1. x projeleri bir [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) `IdentityCookieOptions` parametreyi *accountcontroller.cs* ve *ManageController.cs* öğesine geçirmek için Oluşturucu Ekleme kullanır. Dış cookie kimlik doğrulama düzenine, belirtilen örnekten erişilir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Belirtilen Oluşturucu Ekleme, 2,0 projesinde gereksiz hale gelir ve `_externalCookieScheme` alan silinebilir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1. x projeleri `_externalCookieScheme` alanı şu şekilde kullandı:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2,0 projesinde, yukarıdaki kodu aşağıdaki kodla değiştirin. `IdentityConstants.ExternalScheme`Sabit doğrudan kullanılabilir.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

`SignOutAsync`Aşağıdaki ad alanını içeri aktararak yeni eklenen çağrıyı çözümleyin:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a>IdentityKullanıcı POCO gezintisi özellikleri ekle

Taban `IdentityUser` poco 'nın (düz eskı CLR nesnesi) Entity Framework (EF) temel gezinti özellikleri kaldırılmıştır. 1. x projeniz bu özellikleri kullandıysanız, bunları el ile 2,0 projesine ekleyin:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

EF Core geçişleri çalıştırırken yinelenen yabancı anahtarların oluşmasını engellemek için, `IdentityDbContext` sınıfınızın ' `OnModelCreating` yöntemine (çağrıdan sonra) aşağıdaki ekleyin `base.OnModelCreating();` :

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Getexternalauthenticationdüzenlerini değiştirme

Zaman uyumlu yöntem, `GetExternalAuthenticationSchemes` zaman uyumsuz bir sürüm yararına kaldırılmıştır. 1. x projeleri *denetleyicileri/ManageController. cs* dosyasında aşağıdaki koda sahiptir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Bu yöntem, *Görünümler/Account/Login. cshtml* içinde de görüntülenir:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

2,0 projesinde <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> yöntemini kullanın. *ManageController.cs* içindeki değişiklik aşağıdaki koda benzer:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login. cshtml* dosyasında, `AuthenticationScheme` döngüde erişilen özellik şu `foreach` şekilde değişir `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel özelliği değişikliği

Bir `ManageLoginsViewModel` nesne, `ManageLogins` *ManageController.cs* eyleminde kullanılır. 1. x projelerinde, nesnenin `OtherLogins` özellik dönüş türü `IList<AuthenticationDescription>` . Bu dönüş türü için içeri aktarma gereklidir `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2,0 projesinde, dönüş türü olarak değişir `IList<AuthenticationScheme>` . Bu yeni dönüş türünün içeri aktarma işlemi `Microsoft.AspNetCore.Http.Authentication` bir içeri aktarma ile değiştirilmesi gerekiyor `Microsoft.AspNetCore.Authentication` .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Ek kaynaklar

Daha fazla bilgi için bkz. GitHub 'da [kimlik doğrulama 2,0 sorunu tartışması](https://github.com/aspnet/Security/issues/1338) .
