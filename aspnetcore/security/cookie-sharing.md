---
title: ASP.NET uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma
author: rick-anderson
description: Kimlik doğrulama tanımlama bilgilerini ASP.NET 4. x ve ASP.NET Core uygulamaları arasında paylaşmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776148"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Web siteleri genellikle birlikte çalışan ayrı Web uygulamalarından oluşur. Çoklu oturum açma (SSO) deneyimi sağlamak için, bir site içindeki Web uygulamaları kimlik doğrulama tanımlama bilgilerini paylaşmalıdır. Bu senaryoyu desteklemek için, veri koruma yığını, Katana tanımlama bilgisi kimlik doğrulaması ve ASP.NET Core tanımlama bilgisi kimlik doğrulama biletlerinin paylaşılmasını sağlar.

Aşağıdaki örneklerde:

* Kimlik doğrulama tanımlama bilgisi adı, ortak bir değerine ayarlanır `.AspNet.SharedCookie`.
* , `AuthenticationType` Açıkça ya `Identity.Application` da varsayılan olarak ayarlanır.
* Veri koruma sisteminin veri koruma anahtarlarını (`SharedCookieApp`) paylaşmasını sağlamak için ortak bir uygulama adı kullanılır.
* `Identity.Application`, kimlik doğrulama düzeni olarak kullanılır. Hangi düzenin kullanıldığı, her ne kadar paylaşılan tanımlama bilgisi *uygulamalarında, varsayılan* düzen olarak, ya da açıkça ayarlanarak kullanılması gerekir. Şema, tanımlama bilgilerini şifrelerken ve şifresini çözerken kullanılır, bu nedenle uygulamalar arasında tutarlı bir düzenin kullanılması gerekir.
* Ortak bir [veri koruma anahtarı](xref:security/data-protection/implementation/key-management) depolama konumu kullanılır.
  * ASP.NET Core uygulamalarda, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> anahtar depolama konumunu ayarlamak için kullanılır.
  * .NET Framework uygulamalarda, tanımlama bilgisi kimlik doğrulama ara yazılımı, uygulamasının <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>bir uygulamasını kullanır. `DataProtectionProvider`kimlik doğrulama tanımlama bilgisi yük verilerinin şifrelenmesi ve şifresinin çözülmesi için veri koruma hizmetleri sağlar. `DataProtectionProvider` Örnek, uygulamanın diğer bölümleri tarafından kullanılan veri koruma sisteminden yalıtılmıştır. [DataProtectionProvider. Create (System. ıO. DirectoryInfo, Action\<ıdataprotectionbuilder>),](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) veri <xref:System.IO.DirectoryInfo> koruma anahtarı depolamanın konumunu belirtmek için bir değerini kabul eder.
* `DataProtectionProvider`[Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet paketini gerektirir:
  * ASP.NET Core 2. x uygulamalarında [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)öğesine başvurun.
  * .NET Framework uygulamalar ' da [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)'a bir paket başvurusu ekleyin.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>ortak uygulama adını ayarlar.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Kimlik doğrulama tanımlama bilgilerini ASP.NET Core ile paylaşmaIdentity

ASP.NET Core Identitykullanırken:

* Veri koruma anahtarlarının ve uygulama adının uygulamalar arasında paylaşılması gerekir. Aşağıdaki örneklerde <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> yöntemine ortak bir anahtar depolama konumu verilmiştir. Ortak <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> bir paylaşılan uygulama adı yapılandırmak için kullanın (`SharedCookieApp` aşağıdaki örneklerde). Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.
* Tanımlama bilgileri <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> için veri koruma hizmetini ayarlamak üzere genişletme yöntemini kullanın.
* Varsayılan kimlik doğrulama türü `Identity.Application`.

`Startup.ConfigureServices` içinde:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Kimlik doğrulama tanımlama bilgilerini ASP.NET Core olmadan paylaşmaIdentity

Tanımlama bilgilerini doğrudan ASP.NET Core Identityolmadan kullanırken, ' de `Startup.ConfigureServices`veri korumayı ve kimlik doğrulamasını yapılandırın. Aşağıdaki örnekte, kimlik doğrulama türü olarak `Identity.Application`ayarlanır:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>Farklı temel yollarda tanımlama bilgilerini paylaşma

Bir kimlik doğrulama tanımlama bilgisi, [HttpRequest. PathBase öğesini](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) varsayılan [Cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)olarak kullanır. Uygulamanın tanımlama bilgisinin farklı temel yollarda paylaşılması gerekiyorsa, `Path` geçersiz kılınmalıdır:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Alt etki alanları genelinde tanımlama bilgilerini paylaşma

Etki alanları arasında tanımlama bilgilerini paylaşan uygulamalar barındırırken, [Cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) özelliğinde ortak bir etki alanı belirtin. `first_subdomain.contoso.com` Ve `second_subdomain.contoso.com`gibi `Cookie.Domain` uygulamalar `contoso.com`arasında tanımlama bilgilerini paylaşmak için şunu belirtin `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Bekleyen veri koruma anahtarlarını şifreleyin

Üretim dağıtımları için, ' yi `DataProtectionProvider` , geri kalan anahtarları DPAPI veya X509Certificate ile şifrelemek üzere yapılandırın. Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-encryption-at-rest>. Aşağıdaki örnekte, için <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>bir sertifika parmak izi sağlanır:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulama tanımlama bilgilerini paylaşma

Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullanan ASP.NET 4. x uygulamaları ASP.NET Core tanımlama bilgisi kimlik doğrulama ara yazılımı ile uyumlu kimlik doğrulama tanımlama bilgileri oluşturacak şekilde yapılandırılabilir. Bu, site genelinde sorunsuz bir SSO deneyimi sağlarken, büyük bir sitenin ayrı uygulamalarının çeşitli adımlarda yükseltilmesini sağlar.

Bir uygulama, Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullandığında `UseCookieAuthentication` , projenin *Startup.auth.cs* dosyasında çağırır. Visual Studio 2013 ve sonraki sürümlerde oluşturulan ASP.NET 4. x Web uygulaması projeleri varsayılan olarak Katana tanımlama bilgisi kimlik doğrulama ara yazılımını kullanır. ASP.NET Core `UseCookieAuthentication` uygulamalar için artık kullanılmıyor ve desteklenmese de, `UseCookieAuthentication` Katana tanımlama bilgisi kimlik doğrulama ara yazılımı kullanan bir ASP.NET 4. x uygulamasında çağırma geçerlidir.

ASP.NET 4. x uygulaması .NET Framework 4.5.1 veya üstünü hedeflemelidir. Aksi takdirde, gerekli NuGet paketleri yüklenemeyebilir.

Bir ASP.NET 4. x uygulaması ve bir ASP.NET Core uygulaması arasında kimlik doğrulama tanımlama bilgilerini paylaşmak için, ASP.NET Core uygulamayı [ASP.NET Core uygulamalar arasında kimlik doğrulama tanımlama bilgilerini paylaşma](#share-authentication-cookies-with-aspnet-core-identity) bölümünde belirtilen şekilde yapılandırın ve ardından ASP.NET 4. x uygulamasını aşağıdaki şekilde yapılandırın.

Uygulamanın paketlerinin en son sürümlere güncelleştirildiğinden emin olun. [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) paketini her bir ASP.NET 4. x uygulamasına yükler.

Çağrısını bulun ve değiştirin `UseCookieAuthentication`:

* Tanımlama bilgisi adını ASP.NET Core tanımlama bilgisi kimlik doğrulama ara yazılımı (`.AspNet.SharedCookie` örnekteki) tarafından kullanılan adla eşleşecek şekilde değiştirin.
* Aşağıdaki örnekte, kimlik doğrulama türü olarak `Identity.Application`ayarlanır.
* Başlatılmış bir `DataProtectionProvider` örneğini ortak veri koruma anahtarı depolama konumu olarak belirtin.
* Uygulama adının, kimlik doğrulama tanımlama bilgilerini paylaşan tüm uygulamalar tarafından kullanılan ortak uygulama adına ayarlandığını onaylayın (`SharedCookieApp` örnekte).

Ve `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`ayarlamadıysanız, benzersiz kullanıcıları <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> ayırt eden bir talep olarak ayarlayın.

*App_Start/Startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Bir kullanıcı kimliği oluştururken, kimlik doğrulama`Identity.Application`türü (), içinde tanımlı *App_Start/Startup.auth.cs* `UseCookieAuthentication` içinde tanımlanan `AuthenticationType` türle eşleşmelidir.

*Modeller/ıdentitymodeller. cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Ortak bir kullanıcı veritabanı kullan

Uygulamalar aynı Identity şemayı (sürümünü Identity) kullandıklarında, her uygulama Identity sisteminin aynı kullanıcı veritabanına işaret ettiği konusunda emin olun. Aksi halde kimlik sistemi, kimlik doğrulama tanımlama bilgisindeki bilgileri veritabanındaki bilgilere göre eşleştirmeye çalıştığında çalışma zamanında hatalara neden olur.

Identity Şema uygulamalar arasında farklı olduğunda, genellikle uygulamalar farklı Identity sürümler kullandığından, en son sürümünü temel alan ortak bir veritabanının paylaşılması, yeniden eşleştirmeden ve Identity diğer uygulamanın Identity şemalarında sütun eklemeden mümkün değildir. Ortak bir veritabanının uygulamalar tarafından paylaşılabilmesi için diğer uygulamaları en son Identity sürümü kullanmak üzere yükseltmek genellikle daha etkilidir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
