---
title: Kimlik doğrulamasını cookie ASP.NET uygulamalar arasında paylaşma
author: rick-anderson
description: cookieASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulamanın nasıl paylaşılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 6ac808d11790ae27e82606b442ff215d95b93e41
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631374"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Kimlik doğrulamasını cookie ASP.NET uygulamalar arasında paylaşma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Web siteleri genellikle birlikte çalışan ayrı Web uygulamalarından oluşur. Çoklu oturum açma (SSO) deneyimi sağlamak için, bir site içindeki Web uygulamaları kimlik doğrulama s 'yi paylaşmalıdır cookie . Bu senaryoyu desteklemek için, veri koruma yığını, Katana cookie kimlik doğrulamanın ve ASP.NET Core cookie kimlik doğrulama anahtarlarının paylaşılmasını sağlar.

Aşağıdaki örneklerde:

* Kimlik doğrulama cookie adı, ortak bir değerine ayarlanır `.AspNet.SharedCookie` .
* , `AuthenticationType` `Identity.Application` Açıkça ya da varsayılan olarak ayarlanır.
* Veri koruma sisteminin veri koruma anahtarlarını () paylaşmasını sağlamak için ortak bir uygulama adı kullanılır `SharedCookieApp` .
* `Identity.Application` , kimlik doğrulama düzeni olarak kullanılır. Hangi düzenin kullanıldığı, her ne kadar paylaşılan uygulamalar için *within and across* cookie varsayılan düzen olarak, ya da açıkça ayarlanarak kullanılması gerekir. Şema, şifreleme ve şifre çözme sırasında kullanılır cookie , bu nedenle uygulamalarda tutarlı bir düzenin kullanılması gerekir.
* Ortak bir [veri koruma anahtarı](xref:security/data-protection/implementation/key-management) depolama konumu kullanılır.
  * ASP.NET Core uygulamalarda, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> anahtar depolama konumunu ayarlamak için kullanılır.
  * .NET Framework uygulamalarda, Cookie kimlik doğrulama ara yazılımı bir uygulamasını kullanır <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` , kimlik doğrulaması yük verilerinin şifrelenmesi ve şifresinin çözülmesi için veri koruma hizmetleri sağlar cookie . `DataProtectionProvider`Örnek, uygulamanın diğer bölümleri tarafından kullanılan veri koruma sisteminden yalıtılmıştır. [Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> ),](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) <xref:System.IO.DirectoryInfo> veri koruma anahtar depolaması konumunu belirtmek için bir değerini kabul eder.
* `DataProtectionProvider`[Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet paketini gerektirir:
  * ASP.NET Core 2. x uygulamalarında [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)öğesine başvurun.
  * .NET Framework uygulamalar ' da [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)'a bir paket başvurusu ekleyin.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ortak uygulama adını ayarlar.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Kimlik doğrulaması cookie ile paylaşma ASP.NET Core Identity

Kullanırken ASP.NET Core Identity :

* Veri koruma anahtarlarının ve uygulama adının uygulamalar arasında paylaşılması gerekir. Aşağıdaki örneklerde yöntemine ortak bir anahtar depolama konumu verilmiştir <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> . <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Ortak bir paylaşılan uygulama adı yapılandırmak için kullanın ( `SharedCookieApp` Aşağıdaki örneklerde). Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.
* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>İçin veri koruma hizmetini ayarlamak üzere genişletme yöntemini kullanın cookie .
* Varsayılan kimlik doğrulama türü `Identity.Application` .

`Startup.ConfigureServices` içinde:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Kimlik doğrulama cookie 'leri paylaşma ASP.NET Core Identity

İle cookie doğrudan kullanıldığında ASP.NET Core Identity , ' de veri korumayı ve kimlik doğrulamasını yapılandırın `Startup.ConfigureServices` . Aşağıdaki örnekte, kimlik doğrulama türü olarak ayarlanır `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>cookieFarklı temel yollar genelinde paylaşma

Bir kimlik doğrulaması cookie , varsayılan olarak [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 'i kullanır [ Cookie . Yol](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Uygulamanın cookie farklı temel yollar arasında paylaşılması gerekiyorsa, `Path` geçersiz kılınmalıdır:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Alt cookie etki alanları genelinde paylaşma

Alt etki alanları arasında paylaşan uygulamalar barındırırken, cookie içinde ortak bir etki alanı belirtin [ Cookie . Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) özelliği. Ve gibi cookie uygulamalarında uygulamalar arasında paylaşmak için şunu `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` belirtin `Cookie.Domain` `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Bekleyen veri koruma anahtarlarını şifreleyin

Üretim dağıtımları için, ' yi, `DataProtectionProvider` geri kalan anahtarları DPAPI veya X509Certificate ile şifrelemek üzere yapılandırın. Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-encryption-at-rest>. Aşağıdaki örnekte, için bir sertifika parmak izi sağlanır <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>cookieASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulamasını paylaşma

Katana kimlik doğrulama ara yazılımı kullanan ASP.NET 4. x uygulamaları Cookie cookie , ASP.NET Core Cookie kimlik doğrulama ara yazılımı ile uyumlu olan kimlik doğrulama 'ları oluşturmak için yapılandırılabilir. Bu, site genelinde sorunsuz bir SSO deneyimi sağlarken, büyük bir sitenin ayrı uygulamalarının çeşitli adımlarda yükseltilmesini sağlar.

Bir uygulama Katana Cookie kimlik doğrulama ara yazılımı kullandığında, `UseCookieAuthentication` projenin *Startup.auth.cs* dosyasında çağırır. Visual Studio 2013 ve sonraki sürümlerde oluşturulan ASP.NET 4. x Web uygulaması projeleri varsayılan olarak Katana Cookie kimlik doğrulama ara yazılımını kullanır. `UseCookieAuthentication`ASP.NET Core uygulamalar için artık kullanılmıyor ve desteklenmese de, `UseCookieAuthentication` Katana kimlik doğrulama ara yazılımı kullanan bir ASP.NET 4. x uygulamasında çağırma Cookie geçerlidir.

ASP.NET 4. x uygulaması .NET Framework 4.5.1 veya üstünü hedeflemelidir. Aksi takdirde, gerekli NuGet paketleri yüklenemeyebilir.

cookieBir ASP.NET 4. x uygulaması ve bir ASP.NET Core uygulaması arasında kimlik doğrulamasını paylaşmak için, ASP.NET Core uygulamayı [ cookie ASP.NET Core uygulamalar arasında kimlik doğrulaması](#share-authentication-cookies-with-aspnet-core-identity) oluşturma bölümünde belirtilen şekilde yapılandırın ve ardından ASP.NET 4. x uygulamasını aşağıdaki şekilde yapılandırın.

Uygulamanın paketlerinin en son sürümlere güncelleştirildiğinden emin olun. [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) paketini her bir ASP.NET 4. x uygulamasına yükler.

Çağrısını bulun ve değiştirin `UseCookieAuthentication` :

* cookieAdı ASP.NET Core Cookie kimlik doğrulama ara yazılımı (örnekteki) tarafından kullanılan adla eşleşecek şekilde değiştirin `.AspNet.SharedCookie` .
* Aşağıdaki örnekte, kimlik doğrulama türü olarak ayarlanır `Identity.Application` .
* Başlatılmış bir örneğini `DataProtectionProvider` ortak veri koruma anahtarı depolama konumu olarak belirtin.
* Uygulama adının, kimlik doğrulama s 'yi paylaşan tüm uygulamalar tarafından kullanılan ortak uygulama adına ayarlandığını onaylayın cookie ( `SharedCookieApp` örnekte).

Ve ayarlamadıysanız `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> benzersiz kullanıcıları ayırt eden bir talep olarak ayarlayın.

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

Bir kullanıcı kimliği oluştururken, kimlik doğrulama türü (), içinde `Identity.Application` tanımlı `AuthenticationType` `UseCookieAuthentication` *App_Start/Startup.auth.cs*içinde tanımlanan türle eşleşmelidir.

*Modeller/ Identity Models.cs*:

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

Uygulamalar aynı Identity şemayı (sürümünü Identity ) kullandıklarında, Identity her uygulama sisteminin aynı kullanıcı veritabanına işaret ettiği konusunda emin olun. Aksi takdirde, kimlik sistemi, kimlik doğrulamasında bulunan bilgileri veritabanındaki bilgilere göre eşleştirmeye çalıştığında çalışma zamanında hatalara neden olur cookie .

IdentityŞema uygulamalar arasında farklı olduğunda, genellikle uygulamalar farklı Identity sürümler kullandığından, en son sürümünü temel alan ortak bir veritabanının paylaşılması, Identity yeniden eşleştirmeden ve diğer uygulamanın şemalarında sütun eklemeden mümkün değildir Identity . IdentityOrtak bir veritabanının uygulamalar tarafından paylaşılabilmesi için diğer uygulamaları en son sürümü kullanmak üzere yükseltmek genellikle daha etkilidir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/web-farm>
