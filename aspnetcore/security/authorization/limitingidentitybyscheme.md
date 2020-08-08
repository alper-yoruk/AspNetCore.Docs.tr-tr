---
title: ASP.NET Core belirli bir şemayla yetkilendir
author: rick-anderson
description: Bu makalede, birden çok kimlik doğrulama yöntemleriyle çalışırken kimliğin belirli bir şemayla nasıl sınırlandırılacağını açıklamaktadır.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 66b307a3629e18e49b5bb6e65a156054c0002ba8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022114"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>ASP.NET Core belirli bir şemayla yetkilendir

Tek sayfalı uygulamalar (maça 'Lar) gibi bazı senaryolarda, birden çok kimlik doğrulama yöntemi kullanılması yaygındır. Örneğin, uygulama, cookie JavaScript istekleri için oturum açma ve JWT taşıyıcı kimlik doğrulaması için tabanlı kimlik doğrulaması kullanabilir. Bazı durumlarda, uygulamanın bir kimlik doğrulama işleyicisinin birden çok örneği olabilir. Örneğin, cookie biri temel kimlik içeren iki işleyici ve bir Multi-Factor Authentication (MFA) tetiklendiğinde bir tane oluşturulur. Kullanıcı ek güvenlik gerektiren bir işlem istediği için MFA tetiklenebilir. Kullanıcı MFA gerektiren bir kaynak istediğinde MFA zorlama hakkında daha fazla bilgi için [MFA Ile GitHub sorun koruması bölümüne](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)bakın.

Kimlik doğrulaması sırasında kimlik doğrulama hizmeti yapılandırıldığında bir kimlik doğrulama düzeni adlandırılır. Örnek:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

Yukarıdaki kodda, iki kimlik doğrulama işleyicisi eklenmiştir: biri for cookie a ve taşıyıcı için bir tane.

>[!NOTE]
>Varsayılan düzenin belirtilmesi, `HttpContext.User` özelliğin bu kimliğe ayarlanmakta olması sonucunu sağlar. Bu davranış istenmiyorsa, parametresiz formunu çağırarak devre dışı bırakın `AddAuthentication` .

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Yetkilendir özniteliğiyle düzeni seçme

Uygulama, yetkilendirme noktasında kullanılacak işleyiciyi gösterir. Virgülle ayrılmış bir kimlik doğrulama şeması listesini öğesine geçirerek uygulamanın yetkilendirdiği işleyiciyi seçin `[Authorize]` . `[Authorize]`Öznitelik, varsayılan olarak yapılandırılıp yapılandırılmadığını ne olursa olsun, kullanılacak kimlik doğrulama düzenini veya düzenlerini belirtir. Örnek:

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

Yukarıdaki örnekte, hem cookie hem de taşıyıcı işleyicileri çalışır ve geçerli kullanıcı için bir kimlik oluşturma ve ekleme şansı vardır. Yalnızca tek bir düzen belirterek, karşılık gelen işleyici çalışır.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

Yukarıdaki kodda yalnızca "taşıyıcı" düzenine sahip işleyici çalışır. Herhangi bir cookie tabanlı kimlik yok sayılır.

## <a name="selecting-the-scheme-with-policies"></a>İlkeleri olan düzeni seçme

[İlkede](xref:security/authorization/policies)istenen şemaları belirtmeyi tercih ediyorsanız, `AuthenticationSchemes` ilkenizi eklerken koleksiyonu ayarlayabilirsiniz:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

Yukarıdaki örnekte, "Over18" ilkesi yalnızca "taşıyıcı" işleyicisi tarafından oluşturulan kimliğe göre çalışır. Özniteliğin özelliğini ayarlayarak ilkeyi kullanın `[Authorize]` `Policy` :

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Birden çok kimlik doğrulama şeması kullanma

Bazı uygulamaların birden çok tür kimlik doğrulamasını desteklemesi gerekebilir. Örneğin, uygulamanız Azure Active Directory kullanıcıların kimliğini ve bir kullanıcılar veritabanından kimlik doğrulaması yapabilir. Diğer bir örnek, hem Active Directory Federasyon Hizmetleri (AD FS) hem de Azure Active Directory B2C kullanıcıların kimliğini doğrulayan bir uygulamadır. Bu durumda, uygulamanın birkaç verenler tarafından bir JWT taşıyıcı belirtecini kabul etmesi gerekir.

Kabul etmek istediğiniz tüm kimlik doğrulama düzenlerini ekleyin. Örneğin, aşağıdaki kod `Startup.ConfigureServices` farklı verenler ile ıkı JWT taşıyıcı kimlik doğrulama şeması ekler:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> Varsayılan kimlik doğrulama düzenine yalnızca bir JWT taşıyıcı kimlik doğrulaması kaydedilir `JwtBearerDefaults.AuthenticationScheme` . Ek kimlik doğrulaması, benzersiz bir kimlik doğrulama şemasına kaydedilmelidir.

Bir sonraki adım, varsayılan yetkilendirme ilkesini her iki kimlik doğrulama şemasını kabul edecek şekilde güncelleştirmesidir. Örnek:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

Varsayılan yetkilendirme ilkesi geçersiz kılındığından, bu `[Authorize]` özniteliği denetleyicilerde kullanmak mümkündür. Denetleyici daha sonra ilk veya ikinci veren tarafından JWT veren istekleri kabul eder.

::: moniker-end
