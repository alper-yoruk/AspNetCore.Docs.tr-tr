---
title: ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 ' ye geçiş Identity
author: isaac2004
description: ASP.NET Core 2,0 ' ye üyelik kimlik doğrulaması kullanarak mevcut ASP.NET uygulamalarını geçirmeyi öğrenin Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: de9d1e5f6f595269595212fbab60d12dfd5a29e4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633649"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 ' ye geçiş Identity

İle [Isaac Levi](https://isaaclevin.com) tarafından

Bu makalede üyelik kimlik doğrulaması kullanılarak ASP.NET uygulamaları için veritabanı şemasının ASP.NET Core 2,0 ' ye geçirilmesi gösterilmektedir Identity .

> [!NOTE]
> Bu belge, ASP.NET üyelik tabanlı uygulamalar için veritabanı şemasını için kullanılan veritabanı şemasına geçirmek için gereken adımları sağlar ASP.NET Core Identity . ASP.NET üyelik tabanlı kimlik doğrulamasından ASP.NET 'e geçiş hakkında daha fazla bilgi için Identity bkz. [SQL üyeliğinden mevcut bir uygulamayı ASP.net Identity 'ye geçirme ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Hakkında daha fazla bilgi için ASP.NET Core Identity bkz. [ Identity ASP.NET Core 'ye giriş](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Üyelik şemasının incelenmesi

ASP.NET 2,0 ' den önce, geliştiriciler, uygulamaları için tüm kimlik doğrulama ve yetkilendirme sürecini oluşturmaya eklendi. ASP.NET 2,0 ile üyelik tanıtılmıştı ve ASP.NET Apps içindeki güvenliği işlemek için ortak bir çözüm sağlar. Geliştiriciler artık [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) komutuyla bir şemayı SQL Server veritabanına önyükleyebiliyor. Bu komutu çalıştırdıktan sonra, veritabanında aşağıdaki tablolar oluşturulmuştur.

  ![Üyelik tabloları](identity/_static/membership-tables.png)

Mevcut uygulamaları ASP.NET Core 2,0 ' ye geçirmek için Identity , bu tablolardaki verilerin yeni şema tarafından kullanılan tablolara geçirilmesi gerekir Identity .

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2,0 şeması

ASP.NET Core 2,0, [Identity](/aspnet/identity/index) ASP.NET 4,5 ' de tanıtılan prensibi izler. İlke paylaşılsa da, çerçeveler arasındaki uygulama, ASP.NET Core sürümleri arasında bile farklıdır (bkz. [kimlik doğrulamasını geçirme ve Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

ASP.NET Core 2,0 şemasını görüntülemenin en hızlı yolu, Identity Yeni bir ASP.NET Core 2,0 uygulaması oluşturmaktır. Visual Studio 2017 'de şu adımları izleyin:

1. **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.
1. *Çekirdek Identity örnek*adlı yeni bir **ASP.NET Core Web uygulaması** projesi oluşturun.
1. Açılan listede **ASP.NET Core 2,0** ' i seçin ve ardından **Web uygulaması**' nı seçin. Bu şablon bir [ Razor Sayfalar](xref:razor-pages/index) uygulaması oluşturur. **Tamam**' a tıklamadan önce **kimlik doğrulamasını Değiştir**' e tıklayın.
1. Şablonlar için **bireysel kullanıcı hesapları** seçin Identity . Son olarak **Tamam**' a ve ardından **Tamam**' a tıklayın. Visual Studio, şablonu kullanarak bir proje oluşturur ASP.NET Core Identity .
1. **Tools**  >  Paket Yöneticisi Konsolu (PMC) penceresini açmak için Araçlar**NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** **Package Manager Console** ' nu seçin.
1. PMC 'de Proje köküne gidin ve [Entity Framework (EF) Core](/ef/core) `Update-Database` komutunu çalıştırın.

    ASP.NET Core 2,0, Identity kimlik doğrulama verilerini depolayan veritabanıyla etkileşime geçmek için EF Core kullanır. Yeni oluşturulan uygulamanın çalışması için, bu verileri depolamak üzere bir veritabanı olması gerekir. Yeni bir uygulama oluşturduktan sonra, bir veritabanı ortamında şemayı incelemenize en hızlı yol, [EF Core geçişlerini](/ef/core/managing-schemas/migrations/)kullanarak veritabanını oluşturmaktır. Bu işlem, yerel olarak veya başka bir yerde, bu şemayı taklit eden bir veritabanı oluşturur. Daha fazla bilgi için önceki belgeleri gözden geçirin.

    EF Core komutları * üzerindeappsettings.js*belirtilen veritabanı için bağlantı dizesini kullanır. Aşağıdaki bağlantı dizesi, *ASP-NET-Core-Identity*adlı *localhost* üzerinde bir veritabanını hedefler. Bu ayarda, EF Core bağlantı dizesini kullanacak şekilde yapılandırılır `DefaultConnection` .

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. SQL Server Nesne Gezgini **görüntüle**' yi seçin  >  **SQL Server Object Explorer**. `ConnectionStrings:DefaultConnection` *appsettings.json*özelliğinde belirtilen veritabanı adına karşılık gelen düğümü genişletin.

    `Update-Database`Komut, şema ile belirtilen veritabanını ve uygulama başlatma için gereken tüm verileri oluşturdu. Aşağıdaki görüntüde, önceki adımlarla oluşturulan tablo yapısı gösterilmektedir.

    ![::: No-Loc (kimlik)::: tablolar](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Şemayı geçirme

Her iki üyelik ve için tablo yapılarında ve alanlarında hafif farklar vardır ASP.NET Core Identity . Bu model, ASP.NET ve ASP.NET Core uygulamalarıyla kimlik doğrulama/yetkilendirme için önemli ölçüde değiştirilmiştir. İle hala kullanılan temel nesneler Identity *Kullanıcılar* ve *rollerdir*. *Kullanıcılar*, *Roller*ve Kullanıcı *rolleri*için eşleme tabloları aşağıda verilmiştir.

### <a name="users"></a>Kullanıcılar

|Identity<br>( `dbo.AspNetUsers` ) sütunu  |Tür     |Üyelik<br>( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) sütunu|Tür      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Tüm alan eşlemeleri üyeliğe ait bire bir ilişkiye benzemez ASP.NET Core Identity . Yukarıdaki tablo, varsayılan üyelik kullanıcı şemasını alır ve ASP.NET Core Identity şemayla eşler. Üyelik için kullanılan diğer özel alanların el ile eşlenmesi gerekir. Bu eşlemede, parola ölçütü ve parola salları iki arasında geçiş yapmadan, parola için bir eşleme yoktur. **Parolayı null olarak bırakmanız ve kullanıcılardan parolalarını sıfırlamalarını istemek için önerilir.** İçinde ASP.NET Core Identity , `LockoutEnd` Kullanıcı kilitli ise gelecekte bir tarih olarak ayarlanmalıdır. Bu, geçiş komut dosyasında gösterilmiştir.

### <a name="roles"></a>Roller

|Identity<br>( `dbo.AspNetRoles` ) sütunu|Tür|Üyelik<br>( `dbo.aspnet_Roles` ) sütunu|Tür|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>Kullanıcı Rolleri

|Identity<br>( `dbo.AspNetUserRoles` ) sütunu|Tür|Üyelik<br>( `dbo.aspnet_UsersInRoles` ) sütunu|Tür|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

*Kullanıcılar* ve *Roller*için bir geçiş betiği oluştururken önceki eşleme tablolarına başvurun. Aşağıdaki örnek, bir veritabanı sunucusunda iki veritabanınız olduğunu varsayar. Bir veritabanı var olan ASP.NET üyelik şemasını ve verilerini içerir. Diğer *çekirdek Identity örnek* veritabanı, daha önce açıklanan adımlar kullanılarak oluşturulmuştur. Daha ayrıntılı bilgi için açıklamalar satır içi olarak eklenir.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Önceki betiği tamamladıktan sonra, ASP.NET Core Identity daha önce oluşturulan uygulama, üyelik kullanıcıları ile doldurulur. Kullanıcıların oturum açmadan önce parolalarını değiştirmesi gerekir.

> [!NOTE]
> Üyelik sisteminde kullanıcılara, e-posta adresiyle eşleşmeyen Kullanıcı adları varsa, bu, daha önce oluşturulan uygulamanın buna uyum sağlaması için değişiklikler yapmanız gerekir. Varsayılan şablon `UserName` `Email` , ve için aynı olmasını bekler. Farklı oldukları durumlar için, oturum açma işleminin yerine kullanılacak şekilde değiştirilmesi gerekir `UserName` `Email` .

`PageModel` *Pages\Account\Login.cshtml.cs*adresinde bulunan oturum açma sayfasında, `[EmailAddress]` *e-posta* özelliğinden özniteliği kaldırın. *Kullanıcı adı*olarak yeniden adlandırın. Bu, `EmailAddress` *Görünüm* ve *pagemodel*içinde bahsedilen her yerde bir değişiklik yapılmasını gerektirir. Sonuç aşağıdakine benzer:

 ![Sabit oturum açma](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, SQL üyeliğinden ASP.NET Core 2,0 ' ye kadar olan kullanıcıların bağlantı noktası oluşturmayı öğrendiniz Identity . Hakkında daha fazla bilgi için ASP.NET Core Identity bkz. [giriş Identity ](xref:security/authentication/identity).
