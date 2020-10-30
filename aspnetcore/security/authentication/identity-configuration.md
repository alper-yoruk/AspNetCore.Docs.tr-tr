---
title: Yapılandırma ASP.NET Core Identity
author: AdrienTorris
description: ASP.NET Core IdentityVarsayılan değerleri anlayın ve Identity özellikleri özel değerleri kullanacak şekilde yapılandırmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
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
uid: security/authentication/identity-configuration
ms.openlocfilehash: b11a2d584b7275a9065c9915021ac945823531f8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051999"
---
# <a name="configure-no-locaspnet-core-identity"></a>Yapılandırma ASP.NET Core Identity

ASP.NET Core Identity parola ilkesi, kilitleme ve yapılandırma gibi ayarlar için varsayılan değerleri kullanır cookie . Bu ayarlar sınıfında geçersiz kılınabilir `Startup` .

## <a name="no-locidentity-options"></a>Identity Seçenekler

[ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) sınıfı, sistemi yapılandırmak için kullanılabilen seçenekleri temsil eder Identity . `IdentityOptions` veya çağrıldıktan **sonra** ayarlanması gerekir `AddIdentity` `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>Belirt Identity

[ Identity Options. Claim Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) , aşağıdaki tabloda gösterilen özelliklerle birlikte [talep Identity seçeneklerini](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) belirtir.

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Rol talebi için kullanılan talep türünü alır veya ayarlar. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Güvenlik damga talebi için kullanılan talep türünü alır veya ayarlar. | `AspNet.Identity.SecurityStamp` |
| [Userıdclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Kullanıcı tanımlayıcısı talebi için kullanılan talep türünü alır veya ayarlar. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Kullanıcı adı talebi için kullanılan talep türünü alır veya ayarlar. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Özelliğini

[Passwordsignınasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) yönteminde kilitleme ayarlanır:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Yukarıdaki kod, şablonu temel alır `Login` Identity . 

Kilitleme seçenekleri şu şekilde ayarlanır `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Önceki kod, [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) [ Identity seçeneklerini](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) varsayılan değerlerle ayarlar.

Başarılı bir kimlik doğrulaması başarısız erişim denemesi sayısını sıfırlar ve saati sıfırlar.

[ Identity Options. kilitleme](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) , tabloda gösterilen özelliklerle [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) değerini belirtir.

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Yeni bir kullanıcının kilitlenip kilitlenmeyeceğini belirler. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Bir kilitleme gerçekleştiğinde kullanıcının kilitlediği zaman miktarı. | 5 dakika |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Kilitleme etkinse, bir Kullanıcı kilitlenene kadar başarısız olan erişim girişimlerinin sayısı. | 5 |

### <a name="password"></a>Parola

Varsayılan olarak, Identity Parolaların büyük harf, küçük harf karakter, rakam ve alfasayısal olmayan bir karakter içermesini gerektirir. Parolalar en az altı karakter uzunluğunda olmalıdır.

Parolalar şu şekilde yapılandırılır:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions> içinde `Startup.ConfigureServices` .
* Özellik [ `[StringLength]` öznitelikleri](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) , `Password` Identity [uygulamaya iskli](xref:security/authentication/scaffold-identity)olarak ayarlandığında. `InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) , tabloda gösterilen özelliklerle birlikte [passwordooptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 'ı belirtir.

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Parolada 0-9 arasında bir sayı gerekir. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Parola uzunluğu alt sınırı. | 6 |
| [Requireküçük harf](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Parolada küçük harfli bir karakter gerektirir. | `true` |
| [Talep ırenonalfasayısal](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Parolada alfasayısal olmayan bir karakter gerektirir. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Yalnızca ASP.NET Core 2,0 veya üzeri için geçerlidir.<br><br> Paroladaki ayrı karakter sayısını gerektirir. | 1 |
| [Requirebüyük](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Parolada büyük harfli bir karakter gerektirir. | `true` |

### <a name="sign-in"></a>Oturum açma

Aşağıdaki kod ayarları ayarlar `SignIn` (varsayılan değerlere göre):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) , tabloda gösterilen özelliklerle [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) belirtir.

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Oturum açmak için onaylanan bir e-posta gerekir. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Oturum açmak için onaylanmış bir telefon numarası gerektirir. | `false` |

### <a name="tokens"></a>Belirteçler

[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) , tabloda gösterilen özelliklerle [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) belirler.

| Özellik | Açıklama |
| -------- | ----------- |
| [Kimlik doğrulayan Tortokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | `AuthenticatorTokenProvider`İki öğeli oturum açma işlemlerini kimlik doğrulayıcı ile doğrulamak için kullanılan öğesini alır veya ayarlar. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | `ChangeEmailTokenProvider`E-posta değişiklik onayı e-postalarında kullanılan belirteçleri oluşturmak için kullanılır veya ayarlar. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | `ChangePhoneNumberTokenProvider`Telefon numaralarını değiştirirken kullanılan belirteçler oluşturmak için kullanılan belirteçleri alır veya ayarlar. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Hesap onaylama e-postalarında kullanılan belirteçleri oluşturmak için kullanılan belirteç sağlayıcısını alır veya ayarlar. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Parola sıfırlama e-postalarında kullanılan belirteçleri oluşturmak için kullanılan [ıusertwofactortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) değerini alır veya ayarlar. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Sağlayıcının adı olarak kullanılan anahtarla bir [Kullanıcı belirteci sağlayıcısı](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) oluşturmak için kullanılır. |

### <a name="user"></a>Kullanıcı

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Seçenekler. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) , tabloda gösterilen özelliklerle [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) 'ı belirtir.

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Kullanıcı adında izin verilen karakterler. | abcdefghgpqrstuıxyz<br>ABCDEFGHGPQRSTUıXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Her kullanıcının benzersiz bir e-postasına sahip olmasını gerektirir. | `false` |

### <a name="no-loccookie-settings"></a>Cookie ayarları

Uygulamasının cookie içinde yapılandırma `Startup.ConfigureServices` . [Configureapplication Cookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) , veya çağrıldıktan **sonra** çağrılmalıdır `AddIdentity` `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Daha fazla bilgi için bkz. [ Cookie authenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Parola hasher seçenekleri

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Parola karması seçeneklerini alır ve ayarlar.

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Yeni parolalar Karma oluşturulurken kullanılan uyumluluk modu. Varsayılan olarak olur <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Karma parolanın *Biçim işaretleyicisi* olarak adlandırılan ilk baytı, parolayı karma olarak kullanılan karma algoritmanın sürümünü belirtir. Bir karmaya karşı bir parola doğrulanırken, <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Yöntem ilk bayta göre doğru algoritmayı seçer. Bir istemci, parolayı karma hale almak için hangi algoritma sürümünün kullanıldığına bakılmaksızın kimlik doğrulaması yapabilir. Uyumluluk modunun ayarlanması *yeni parolaların* karmasını etkiler. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | PBKDF2 kullanarak parolaları karmalama sırasında kullanılan yineleme sayısı. Bu değer yalnızca, <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> olarak ayarlandığında kullanılır <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Değer pozitif bir tamsayı olmalı ve varsayılan olarak olarak ayarlanmalıdır `10000` . |

Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> içinde olarak ayarlanır `12000` `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Genel olarak tüm kullanıcıların kimliğinin doğrulanmasını gerektir

[!INCLUDE[](~/includes/requireAuth.md)]