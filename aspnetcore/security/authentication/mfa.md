---
title: ASP.NET Core 'de çok faktörlü kimlik doğrulaması
author: damienbod
description: ASP.NET Core uygulamasında Multi-Factor Authentication (MFA) ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: e224f947335ea8ea6ed8887dfadb52202bfd7866
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009511"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>ASP.NET Core 'de çok faktörlü kimlik doğrulaması

, [Davmien Bowden](https://github.com/damienbod) tarafından

Multi-Factor Authentication (MFA), bir kullanıcının ek tanımlama biçimleri için oturum açma olayında istediği bir işlemdir. Bu istem, bir Cellphone 'dan bir kod girmek, FIDO2 anahtarı kullanmak veya parmak izi taraması sağlamak olabilir. İkinci bir kimlik doğrulama formu gerektirdiğinde güvenlik geliştirilmiştir. Ek faktör bir saldırgan tarafından kolayca alınıp çoğaltılamaz.

Bu makalede aşağıdaki alanlarda yer verilmiştir:

* MFA nedir ve MFA akışlarının kullanılması önerilir
* Kullanarak yönetim sayfaları için MFA 'yı yapılandırma ASP.NET Core Identity
* OpenID Connect sunucusuna MFA oturum açma gereksinimi gönderin
* ASP.NET Core OpenID Connect istemcisini MFA gerektir olarak zorla

## <a name="mfa-2fa"></a>MFA, 2FA

MFA, bildiğiniz bir şey, sahip olduğunuz bir şey veya kullanıcının kimlik doğrulaması için Biyometri doğrulaması gibi bir kimlik için en az iki veya daha fazla kanıt türü gerektirir.

İki öğeli kimlik doğrulaması (2FA) MFA 'nın bir alt kümesi gibidir, ancak MFA 'nın kimliği kanıtlamak için iki veya daha fazla faktör gerektirmesine yönelik fark.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (zamana bağlı bir kerelik parola algoritması)

TOTP kullanarak MFA, kullanarak desteklenen bir uygulama ASP.NET Core Identity . Bu, aşağıdakiler dahil olmak üzere tüm uyumlu Doğrulayıcı uygulamaları ile birlikte kullanılabilir:

* Microsoft Authenticator uygulaması
* Google Authenticator uygulaması

Uygulama ayrıntıları için aşağıdaki bağlantıya bakın:

[ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 veya passwordless

FIDO2 Şu anda:

* MFA elde etmenin en güvenli yolu.
* Sızdırma saldırılarına karşı koruyan tek MFA akışı.

Mevcut olduğunda ASP.NET Core doğrudan FIDO2 desteklemez. FIDO2, MFA veya parolasız akışlar için kullanılabilir.

Azure Active Directory, FIDO2 ve parolasız akışlar için destek sağlar. Daha fazla bilgi için bkz. [Azure Active Directory Için passwordless kimlik doğrulama seçenekleri](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>MFA SMS

SMS ile MFA, parola kimlik doğrulaması (tek etken) ile karşılaştırıldığında güvenlik çarpanını büyük ölçüde artırır. Ancak, SMS 'nin ikinci bir faktör olarak kullanılması artık önerilmez. Bu tür bir uygulama için çok sayıda bilinen saldırı vektörü var.

[NıST yönergeleri](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a>Kullanarak yönetim sayfaları için MFA 'yı yapılandırma ASP.NET Core Identity

MFA, kullanıcıların bir uygulama içindeki hassas sayfalara erişmelerine zorlanabilir ASP.NET Core Identity . Bu, farklı kimlikler için farklı erişim düzeylerinin bulunduğu uygulamalar için yararlı olabilir. Örneğin, kullanıcılar bir parola oturum açma kullanarak profil verilerini görüntüleyebilir, ancak yönetim sayfalarına erişmek için bir yöneticinin MFA 'yı kullanması gerekir.

### <a name="extend-the-login-with-an-mfa-claim"></a>Bir MFA talebi ile oturum açmayı genişletme

Tanıtım kodu, ve sayfaları ile ASP.NET Core kullanarak Identity ayarlanır Razor . `AddIdentity`Yöntemi bir yerine kullanılır, bu `AddDefaultIdentity` nedenle `IUserClaimsPrincipalFactory` başarılı bir oturum açma işleminden sonra kimliğe talep eklemek için bir uygulama kullanılabilir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

`AdditionalUserClaimsPrincipalFactory`Sınıfı, `amr` talebi yalnızca başarılı bir oturum açma işleminden sonra Kullanıcı taleplerine ekler. Talebin değeri veritabanından okundu. Talep buraya eklenir, çünkü kimlik MFA ile oturum açtıysa Kullanıcı yalnızca daha yüksek korumalı görünüme erişir. Veritabanı görünümü talebi kullanmak yerine doğrudan veritabanından okunduktan sonra MFA 'yı etkinleştirdikten sonra doğrudan MFA olmadan görünüme erişebilirsiniz.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

IdentityHizmet kurulumu sınıfında değiştiği için `Startup` , Identity güncelleştirilmeleri gereken düzenler. IdentitySayfaları uygulamaya dönüştürün. * Identity /Account/Manage/_Layout. cshtml* dosyasında düzeni tanımlayın.

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Ayrıca, sayfalardaki tüm yönetim sayfalarını için Düzen ata Identity :

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Yönetim sayfasında MFA gereksinimini doğrulama

Yönetim Razor sayfası, kullanıcının MFA kullanarak oturum açtığını doğrular. `OnGet`Yönteminde, kimlik Kullanıcı taleplerine erişmek için kullanılır. `amr`Talep değer için denetlenir `mfa` . Kimliğin bu talebi yoksa veya bu talep yoksa `false` , sayfa MFA 'Yı etkinleştir sayfasına yeniden yönlendirir. Bu, Kullanıcı zaten MFA olmadan oturum açtığından, bu mümkündür.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Kullanıcı oturum açma bilgilerini değiştirmek için UI mantığı

Başlatma sırasında bir yetkilendirme ilkesi eklenmiştir. İlke, bu `amr` değere sahip talep gerektiriyor `mfa` .

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Bu ilke daha sonra, `_Layout` **yönetici** menüsünü uyarı ile göstermek veya gizlemek için görünümünde kullanılabilir:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Kimlik MFA kullanılarak oturum açtıysa, **yönetici** menüsü araç ipucu uyarısı olmadan görüntülenir. Kullanıcı MFA olmadan oturum açtığında, **yönetici (etkin değil)** menüsü, kullanıcıya bildiren araç ipucuyla birlikte görüntülenir (uyarı hakkında).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Kullanıcı MFA olmadan oturum açarsa, uyarı görüntülenir:

![Yönetici MFA kimlik doğrulaması](mfa/_static/identitystandalonemfa_01.png)

**Yönetici** bağlantısına tıklarken Kullanıcı MFA etkinleştirme görünümüne yönlendirilir:

![Yönetici MFA kimlik doğrulamasını etkinleştirir](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>OpenID Connect sunucusuna MFA oturum açma gereksinimi gönderin 

`acr_values`Parametresi, `mfa` gerekli değeri istemciden bir kimlik doğrulaması isteğindeki sunucuya geçirmek için kullanılabilir.

> [!NOTE]
> `acr_values`Bunun çalışması için, parametrenin OpenID Connect sunucusunda işlenmesi gerekir.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core istemcisi

RazorOpenID Connect istemci uygulaması ASP.NET Core sayfaları, `AddOpenIdConnect` OpenID Connect sunucusunda oturum açmak için yöntemini kullanır. `acr_values`Parametresi değeri ile ayarlanır `mfa` ve kimlik doğrulama isteğiyle birlikte gönderilir. `OpenIdConnectEvents`Bunu eklemek için kullanılır.

Önerilen `acr_values` parametre değerleri için bkz. [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a>Örnek OpenID Connect Identity Server 4 Server with ASP.NET Core Identity

MVC görünümleriyle kullanılarak uygulanan OpenID Connect sunucusunda ASP.NET Core Identity , *ErrorEnable2FA. cshtml* adlı yeni bir görünüm oluşturulur. Görünüm:

* , Identity MFA gerektiren bir uygulamadan geliyorsa, ancak Kullanıcı ' de bunu etkinleştirmediğinde görüntüler Identity .
* Kullanıcıya bildirir ve bunu etkinleştirmek için bir bağlantı ekler.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

`Login`Yönteminde, `IIdentityServerInteractionService` `_interaction` OpenID Connect istek parametrelerine erişmek için arabirim uygulama kullanılır. `acr_values`Parametresi, özelliği kullanılarak erişilir `AcrValues` . İstemci bu `mfa` ayarı kümesiyle gönderdiğinden, bu daha sonra denetlenebilir.

MFA gerekliyse ve içindeki Kullanıcı ASP.NET Core Identity MFA 'yı etkinleştirmişse, oturum açma işlemi devam eder. Kullanıcının MFA özelliği etkin olmadığında, Kullanıcı *ErrorEnable2FA. cshtml*özel görünümüne yönlendirilir. Ardından ASP.NET Core Identity Kullanıcı oturumunu kapatır.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

`ExternalLoginCallback`Yöntemi yerel oturum açma gibi çalışmaktadır Identity . `AcrValues`Özelliği değeri için denetlenir `mfa` . `mfa`Değer varsa,, oturum açma tamamlanmadan önce MFA zorlanır (örneğin, görünüme yeniden yönlendirilir `ErrorEnable2FA` ).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Kullanıcı zaten oturum açmışsa istemci uygulaması:

* Talebi hala doğrular `amr` .
* MFA 'yı görünümün bir bağlantısıyla ayarlayabilir ASP.NET Core Identity .

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>ASP.NET Core OpenID Connect istemcisini MFA gerektir olarak zorla

Bu örnek Razor , OpenID Connect kullanan bir ASP.NET Core sayfası uygulamasının, KULLANıCıLARıN MFA kullanarak kimlik doğrulamasından sahip olduğunu gerektirdiğini gösterir.

MFA gereksinimini doğrulamak için bir `IAuthorizationRequirement` gereksinim oluşturulur. Bu, MFA gerektiren bir ilke kullanılarak sayfalara eklenecektir.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

, `AuthorizationHandler` `amr` Talebi kullanacak ve değeri kontrol edecek şekilde uygulanır `mfa` . , `amr` `id_token` Başarılı bir kimlik doğrulaması içinde döndürülür ve [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) belirtiminde tanımlanan birçok farklı değere sahip olabilir.

Döndürülen değer, kimliğin kimlik doğrulamasının ve OpenID Connect sunucu uygulamasında nasıl doğrulandığına bağlıdır.

, `AuthorizationHandler` Gereksinimi kullanır `RequireMfa` ve `amr` talebi doğrular. OpenID Connect sunucusu, ile Server4 kullanılarak uygulanabilir Identity ASP.NET Core Identity . Bir Kullanıcı TOTP kullanarak oturum açtığında, `amr` talep BIR MFA değeri ile döndürülür. Farklı bir OpenID Connect sunucu uygulamasının veya farklı bir MFA türünün kullanılması durumunda `amr` talep ya da olabilir, farklı bir değere sahip olur. Bu kod de kabul etmek için genişletilmelidir.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

`Startup.ConfigureServices`Yönteminde, `AddOpenIdConnect` yöntemi varsayılan zorluk düzeni olarak kullanılır. Talebi denetlemek için kullanılan yetkilendirme işleyicisi, `amr` Denetim kapsayıcısının INVERSION öğesine eklenir. Daha sonra, gereksinimi ekleyen bir ilke oluşturulur `RequireMfa` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Bu ilke daha sonra Razor sayfada gerektiği şekilde kullanılır. İlke, tüm uygulama için genel olarak eklenebilir.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Kullanıcı MFA olmadan kimlik doğrulaması gerçekleştiriyorsa, `amr` talep muhtemelen bir değere sahip olur `pwd` . İsteğin sayfaya erişim yetkisi yok. Varsayılan değerleri kullanarak, Kullanıcı *Hesap/Accessreddedildi* sayfasına yönlendirilir. Bu davranış değiştirilebilir veya kendi özel mantığınızı buradan uygulayabilirsiniz. Bu örnekte, geçerli kullanıcının, hesabı için MFA ayarlayabilmesi için bir bağlantı eklenir.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Artık yalnızca MFA ile kimlik doğrulaması yapan kullanıcılar sayfaya veya Web sitesine erişebilir. Farklı MFA türleri kullanılıyorsa veya 2FA tamam ise, `amr` talebin farklı değerleri olur ve doğru şekilde işlenmesi gerekir. Farklı OpenID Connect sunucuları Ayrıca bu talep için farklı değerler döndürür ve [kimlik doğrulama yöntemi başvuru değerleri](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) belirtimini izlemeyebilir.

MFA olmadan oturum açarken (örneğin, yalnızca bir parola kullanarak):

* , `amr` Şu `pwd` değere sahiptir:

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* Erişim reddedildi:

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

Alternatif olarak, ile OTP kullanarak oturum açma Identity :

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes)
* [Azure Active Directory için passwordless kimlik doğrulama seçenekleri](/azure/active-directory/authentication/concept-authentication-passwordless)
* [.NET kullanarak FIDO2/WebAuthn kanıtlama ve onaylama için FIDO2 .NET kitaplığı](https://github.com/abergs/fido2-net-lib)
* [WebAuthn başar](https://github.com/herrjemand/awesome-webauthn)
