---
title: ASP.NET Core siteler arası Istek sahteciliği (XSRF/CSRF) saldırılarını önle
author: steve-smith
description: Kötü amaçlı bir Web sitesinin istemci tarayıcısı ile uygulama arasındaki etkileşimi etkileyebilecek Web uygulamalarına karşı saldırıları nasıl önleyebileceğiniz hakkında daha fazla öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 4e7e7a89daaee533f648efdb2c621399225f57be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774010"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core siteler arası Istek sahteciliği (XSRF/CSRF) saldırılarını önle

By [Rick Anderson](https://twitter.com/RickAndMSFT), [fiyaz hasan](https://twitter.com/FiyazBinHasan)ve [Steve Smith](https://ardalis.com/)

Siteler arası istek sahteciliği (XSRF veya CSRF olarak da bilinir), kötü amaçlı bir Web uygulamasının, bir istemci tarayıcısı ile bu tarayıcıya güvenen bir Web uygulaması arasındaki etkileşimi etkileyebilecek Web 'de barındırılan uygulamalara karşı bir saldırıdır. Web tarayıcıları her bir Web sitesi isteğiyle otomatik olarak bazı kimlik doğrulama belirteçleri gönderdikleri için bu saldırılar mümkündür. Bu güvenlik düzeyi, *tek tıklamayla saldırı* veya *oturum* adı olarak da bilinir çünkü saldırı, kullanıcının önceden kimliği doğrulanmış oturumunun avantajlarından yararlanır.

CSRF saldırılarına bir örnek:

1. Kullanıcı, Forms kimlik `www.good-banking-site.com` doğrulaması kullanarak oturum açar. Sunucu, kullanıcının kimliğini doğrular ve kimlik doğrulama tanımlama bilgisi içeren bir yanıt yayınlar. Site, geçerli bir kimlik doğrulama tanımlama bilgisiyle aldığı herhangi bir isteğe güvendiğinden saldırıya açıktır.
1. Kullanıcı kötü amaçlı bir siteyi ziyaret ettiğinde `www.bad-crook-site.com`.

   Kötü amaçlı site `www.bad-crook-site.com`, aşağıdakine benzer bir HTML formu içerir:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Formun kötü amaçlı siteye değil `action` , güvenlik açığı bulunan siteye gönderdiğine dikkat edin. Bu, CSRF 'nin "siteler arası" parçasıdır.

1. Kullanıcı Gönder düğmesini seçer. Tarayıcı isteği yapar ve istenen etki alanı için kimlik doğrulama tanımlama bilgisini otomatik olarak ekler `www.good-banking-site.com`.
1. İstek, kullanıcının kimlik doğrulama `www.good-banking-site.com` bağlamıyla sunucuda çalışır ve kimliği doğrulanmış bir kullanıcının gerçekleştirmesine izin verilen herhangi bir eylemi gerçekleştirebilir.

Kullanıcının formu göndermek için düğmeyi seçtiği senaryoya ek olarak, kötü amaçlı site şunları verebilir:

* Formu otomatik olarak gönderen bir komut dosyası çalıştırın.
* Form gönderimini bir AJAX isteği olarak gönderin.
* Formu CSS kullanarak gizleyin.

Bu alternatif senaryolar, ilk olarak kötü amaçlı siteyi ziyaret eden kullanıcıdan herhangi bir eylem veya giriş gerektirmez.

HTTPS kullanmak CSRF saldırılarına engel olmaz. Kötü amaçlı site, güvenli olmayan `https://www.good-banking-site.com/` bir istek gönderebilmesini mümkün olduğunca kolay bir şekilde gönderebilir.

Bazı saldırılar, GET isteklerine yanıt veren uç noktaları, bu durumda eylemi gerçekleştirmek için bir resim etiketi kullanılabilir. Bu saldırı biçimi, görüntülere izin veren ancak JavaScript 'ı engelleyen Forum sitelerinde yaygındır. Değişkenlerin veya kaynakların değiştirildiği, GET isteklerinde durumu değiştiren uygulamalar kötü niyetli saldırılara açıktır. **Durumu değiştirme isteklerini güvenli olmayan GET istekleri. Bir GET isteğindeki durumu hiçbir şekilde değiştirmemek en iyi uygulamadır.**

CSRF saldırıları, kimlik doğrulaması için tanımlama bilgileri kullanan Web uygulamalarına karşı şu nedenle mümkündür:

* Tarayıcılar bir Web uygulaması tarafından verilen tanımlama bilgilerini depolar.
* Depolanan tanımlama bilgileri, kimliği doğrulanmış kullanıcılar için oturum tanımlama bilgilerini içerir.
* Tarayıcılar, bir etki alanı ile ilişkili tüm tanımlama bilgilerini, uygulama isteğinin tarayıcıdan oluşturulma şeklinden bağımsız olarak her istek için Web uygulamasına gönderir.

Ancak, CSRF saldırıları, tanımlama bilgilerini kötüye ile sınırlı değildir. Örneğin, temel ve Özet kimlik doğrulaması da savunmasız olacaktır. Bir Kullanıcı temel veya Özet kimlik doğrulamasıyla oturum açtıktan sonra, oturum&dagger; sona erene kadar tarayıcı kimlik bilgilerini otomatik olarak gönderir.

&dagger;Bu bağlamda *oturum* , kullanıcının kimlik doğrulaması sırasında istemci tarafı oturumu anlamına gelir. Sunucu tarafı oturumları veya [ASP.NET Core oturum ara yazılımı](xref:fundamentals/app-state)ile ilgisi yoktur.

Kullanıcılar, önlemler alarak CSRF güvenlik açıklarına karşı koruma sağlayabilir:

* Web Apps 'in kullanımı bittiğinde oturumunuzu kapatın.
* Tarayıcı tanımlama bilgilerini düzenli aralıklarla temizleyin.

Ancak, CSRF güvenlik açıkları son kullanıcıdan değil, Web uygulamasıyla ilgili bir sorundur.

## <a name="authentication-fundamentals"></a>Kimlik doğrulama temelleri

Tanımlama bilgisi tabanlı kimlik doğrulama, popüler bir kimlik doğrulama biçimidir. Belirteç tabanlı kimlik doğrulama sistemleri, özellikle tek sayfalı uygulamalar (maça 'Lar) için popülerlik olarak büyüyordur.

### <a name="cookie-based-authentication"></a>Tanımlama bilgisi tabanlı kimlik doğrulaması

Bir Kullanıcı Kullanıcı adını ve parolasını kullanarak kimliğini doğruladığında, kimlik doğrulama ve yetkilendirme için kullanılabilecek bir kimlik doğrulama bileti içeren bir belirteç verilirler. Belirteç, istemcinin yaptığı her istekte eşlik eden bir tanımlama bilgisi olarak depolanır. Bu tanımlama bilgisinin oluşturulması ve doğrulanması, tanımlama bilgisi kimlik doğrulama ara yazılımı tarafından gerçekleştirilir. [Ara yazılım](xref:fundamentals/middleware/index) , bir Kullanıcı sorumlusunu şifreli bir tanımlama bilgisine seri hale getirir. Sonraki isteklerde, ara yazılım tanımlama bilgisini doğrular, sorumluyu yeniden oluşturur ve sorumluyu [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext) [Kullanıcı](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) özelliğine atar.

### <a name="token-based-authentication"></a>Belirteç tabanlı kimlik doğrulaması

Bir kullanıcının kimliği doğrulandığında, bunlar bir belirteç (antibir belirteç değil) olarak verilirler. Belirteç, [talepler](/dotnet/framework/security/claims-based-identity-model) formundaki Kullanıcı bilgilerini veya uygulamayı uygulamada tutulan Kullanıcı durumuna işaret eden bir başvuru belirtecini içerir. Bir kullanıcı kimlik doğrulaması gerektiren bir kaynağa erişmeyi denediğinde, belirteç, taşıyıcı belirteç biçiminde ek bir yetkilendirme üstbilgisiyle uygulamaya gönderilir. Bu, uygulamanın durum bilgisiz olmasını sağlar. Sonraki her istekte, belirteç sunucu tarafı doğrulama isteğine geçirilir. Bu belirteç *şifrelenmez*; *kodlandı*. Sunucusunda, bilgilerine erişmek için belirtecin kodu çözülür. Sonraki isteklere belirteç göndermek için, belirteci tarayıcının yerel deposunda depolayın. Belirteç tarayıcının yerel deposunda depolanıyorsa, CSRF güvenlik açığıyla ilgilenmeyin. CSRF, belirteç bir tanımlama bilgisinde depolandığında sorun teşkil ediyor. Daha fazla bilgi için bkz. GitHub sorunu [Spa kodu örneği iki tanımlama bilgisi ekler](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Tek bir etki alanında barındırılan birden çok uygulama

Paylaşılan barındırma ortamları, oturum ele geçirme, oturum açma CSRF ve diğer saldırılara karşı savunmasız kalır.

Ve `example1.contoso.net` `example2.contoso.net` farklı konaklar olsa da, `*.contoso.net` etki alanı altındaki konaklar arasında örtülü bir güven ilişkisi vardır. Bu örtük güven ilişkisi, güvenilmeyen ana bilgisayarların birbirlerinin tanımlama bilgilerini etkilemesini sağlar (AJAX isteklerini yöneten aynı kaynak ilkeleri HTTP tanımlama bilgilerine uygulanmaz).

Aynı etki alanı üzerinde barındırılan uygulamalar arasında güvenilen tanımlama bilgileriyle faydalanan saldırılar, etki alanlarının paylaşılmasından engellenebilir. Her uygulama kendi etki alanında barındırıldığı zaman, açıktan yararlanılabilmesi için örtük bir tanımlama bilgisi güven ilişkisi yoktur.

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core antiforgery yapılandırması

> [!WARNING]
> ASP.NET Core, [ASP.NET Core veri korumasını](xref:security/data-protection/introduction)kullanarak antiforgery uygular. Veri koruma yığınının bir sunucu grubunda çalışacak şekilde yapılandırılması gerekir. Daha fazla bilgi için bkz. [veri korumayı yapılandırma](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

Aşağıdaki API 'lerden biri çağrıldığında, antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir `Startup.ConfigureServices`:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

' De çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> , antiforgery ara yazılımı [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenir`Startup.ConfigureServices`

::: moniker-end

ASP.NET Core 2,0 veya sonraki sürümlerde [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır. Bir Razor dosyasında aşağıdaki biçimlendirme, antiforgery belirteçlerini otomatik olarak oluşturur:

```cshtml
<form method="post">
    ...
</form>
```

Benzer şekilde, [ıhtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) , formun yöntemi get değilse, varsayılan olarak antiforgery belirteçleri oluşturur.

HTML form öğeleri için antiforgery belirteçlerinin otomatik olarak oluşturulması, `<form>` etiketi `method="post"` özniteliği içerdiğinde ve aşağıdakilerden biri doğru olduğunda gerçekleşir:

* Action özniteliği boş (`action=""`).
* Eylem özniteliği sağlanmadı (`<form method="post">`).

HTML form öğeleri için antiforgery belirteçlerinin otomatik nesli devre dışı bırakılabilir:

* `asp-antiforgery` Özniteliği ile antiforgery belirteçlerini açıkça devre dışı bırakın:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Form öğesi etiket Yardımcısı! geri alma simgesi kullanılarak etiket yardımcılarını [kabul](xref:mvc/views/tag-helpers/intro#opt-out)etmedi:

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* `FormTagHelper` Görünümden ' i kaldırın. , `FormTagHelper` Razor görünümüne aşağıdaki yönergeyi ekleyerek bir görünümden kaldırılabilir:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor Pages](xref:razor-pages/index) , XSRF/CSRF 'den otomatik olarak korunur. Daha fazla bilgi için bkz. [XSRF/CSRF ve Razor Pages](xref:razor-pages/index#xsrf).

CSRF saldırılarına karşı savunma için en yaygın yaklaşım, *Eşitleyici belirteç deseninin* (STP) kullanılması. Kullanıcı form verileri içeren bir sayfa istediğinde STP kullanılır:

1. Sunucu, geçerli kullanıcının kimliğiyle ilişkili bir belirteci istemciye gönderir.
1. İstemci doğrulama için belirteci sunucuya geri gönderir.
1. Sunucu kimliği doğrulanmış kullanıcının kimliğiyle eşleşmeyen bir belirteç alırsa istek reddedilir.

Belirteç benzersizdir ve tahmin edilemez. Belirteç Ayrıca bir istek dizisinin doğru sıralamasını sağlamak için kullanılabilir (örneğin, sayfa 1 &ndash; sayfa 2 &ndash; sayfa 3). ASP.NET Core MVC ve Razor Pages şablonlarındaki tüm formlar, antiforgery belirteçleri oluşturur. Aşağıdaki görünüm örnekleri, antiforgery belirteçleri oluşturur:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

HTML Yardımcısı `<form>` [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)ile etiket yardımcıları kullanmadan bir öğeye açık bir şekilde antiforgery belirteci ekleyin:

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

Yukarıdaki durumların her birinde, ASP.NET Core şuna benzer bir gizli form alanı ekler:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core, antiforgery belirteçleriyle çalışmak için üç [filtre](xref:mvc/controllers/filters) içerir:

* [Validateantiforgeryıtoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [Oto Validateantiforgeryıtoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [Ignoreantiforgeri belirteci](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Antiforgery seçenekleri

`Startup.ConfigureServices`İçindeki [antiforgery seçeneklerini](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) özelleştirin:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Using [ıebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) sınıfının özelliklerini kullanarak `Cookie` antiforgery özelliklerini ayarlayın.

| Seçenek | Açıklama |
| ------ | ----------- |
| [Bilgilerinin](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Antiforgery tanımlama bilgilerini oluşturmak için kullanılan ayarları belirler. |
| [Form alanadı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery sistemi tarafından kullanılan üstbilginin adı. Varsa `null`, sistem yalnızca form verilerini dikkate alır. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | `X-Frame-Options` Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir. Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur. Varsayılan olarak `false`olur. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Seçenek | Açıklama |
| ------ | ----------- |
| [Bilgilerinin](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Antiforgery tanımlama bilgilerini oluşturmak için kullanılan ayarları belirler. |
| [Pişirme etki alanı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Tanımlama bilgisinin etki alanı. Varsayılan olarak `null`olur. Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak. Önerilen alternatif, Cookie. Domain ' dir. |
| [Tanımlama bilgisi adı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Tanımlama bilgisinin adı. Ayarlanmamışsa, sistem [Defaultpişirme ıeprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("ile başlayan benzersiz bir ad oluşturur. AspNetCore. Antiforgery. "). Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak. Önerilen alternatif, Cookie.Name ' dir. |
| [Tanımlama, ıepath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Tanımlama bilgisinde ayarlanan yol. Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak. Önerilen alternatif, Cookie. Path ' dir. |
| [Form alanadı](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Görünümlerde antiforgery belirteçlerini işlemek için antiforgery sistemi tarafından kullanılan gizli form alanının adı. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Antiforgery sistemi tarafından kullanılan üstbilginin adı. Varsa `null`, sistem yalnızca form verilerini dikkate alır. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Antiforgery sistemi için HTTPS 'nin gerekli olup olmadığını belirtir. `true`, Https olmayan istekler başarısız olur. Varsayılan olarak `false`olur. Bu özellik artık kullanılmıyor ve gelecek bir sürümde kaldırılacak. Önerilen alternatif, Cookie. SecurePolicy ' i ayarlanmakta. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | `X-Frame-Options` Üstbilginin oluşturulup oluşturulmayacağını bastırıp gizlenmeyeceğini belirtir. Varsayılan olarak, üst bilgi "SAMEORIGIN" değeri ile oluşturulur. Varsayılan olarak `false`olur. |

::: moniker-end

Daha fazla bilgi için bkz. [tanımlama, ıeauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Iantiforgery ile antiforgery özelliklerini yapılandırma

[Iantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) , antiforgery özelliklerini YAPıLANDıRMAK için API sağlar. `IAntiforgery`, `Configure` `Startup` sınıfının yönteminde istenebilir. Aşağıdaki örnek, bir antiforgery belirteci oluşturmak ve yanıtta bir tanımlama bilgisi olarak (Bu konunun ilerleyen kısımlarında açıklanan varsayılan angular adlandırma kuralını kullanarak) bir uygulama ana sayfasından bir ara yazılım kullanır:

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Antiforgery doğrulaması gerektir

[Validateantiforgeritoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) , tek bir eyleme, denetleyiciye veya küresel olarak uygulanabilen bir eylem filtresidir. Bu filtre uygulanmış eylemlere yapılan istekler, istek geçerli bir antiforgery belirteci içermiyorsa engellenir.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

`ValidateAntiForgeryToken` ÖZNITELIK, http get istekleri de dahil olmak üzere, işaret eden eylem yöntemlerine istek için bir belirteç gerektirir. `ValidateAntiForgeryToken` Öznitelik, uygulamanın denetleyicileri arasında uygulanırsa, `IgnoreAntiforgeryToken` özniteliğiyle geçersiz kılınabilir.

> [!NOTE]
> ASP.NET Core, istekleri otomatik olarak almak için antiforgery belirteçleri eklemeyi desteklemez.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Yalnızca güvenli olmayan HTTP metotları için antiforgery belirteçlerini otomatik olarak doğrula

ASP.NET Core uygulamalar güvenli HTTP yöntemleri (GET, HEAD, OPTIONS ve TRACE) için antiforgery belirteçleri oluşturmaz. `ValidateAntiForgeryToken` Özniteliği genel olarak uygulamak ve ardından `IgnoreAntiforgeryToken` özniteliklerle geçersiz kılmak yerine, [oto Validateantiforgerontoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) özniteliği kullanılabilir. Bu öznitelik, `ValidateAntiForgeryToken` özniteliğiyle aynı şekilde çalışır, ancak aşağıdaki http yöntemlerini kullanarak yapılan isteklere belirteç gerektirmez:

* GET
* HEAD
* Seçenekler
* TRACE

API olmayan senaryolara yönelik `AutoValidateAntiforgeryToken` olarak kullanılması önerilir. Bu, varsayılan olarak POST eylemlerinin korunmasını sağlar. Diğer bir deyişle, tek tek eylem yöntemlerine uygulanmamışsa `ValidateAntiForgeryToken` , varsayılan olarak antiforgery belirteçlerini yok saymanız gerekir. Bu senaryoda, bir POST eylemi yönteminin korumasız olarak bırakılması, uygulamanın CSRF saldırılarına karşı savunmasız bırakılması daha yüksektir. Tüm gönderimler, antiforgery belirtecini göndermelidir.

API 'lerin, belirtecin tanımlama bilgisi olmayan bölümünü göndermek için otomatik bir mekanizması yoktur. Uygulama, büyük olasılıkla istemci kodu uygulamasına bağlıdır. Bazı örnekler aşağıda gösterilmiştir:

Sınıf düzeyi örnek:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Genel örnek:

::: moniker range="< aspnetcore-3.0"

servislere. AddMvc (Options => seçenekleri. Filters. Add (New, oto Validateantiforgeryıtokenattribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Küresel veya denetleyici antiforgery özniteliklerini geçersiz kıl

[Ignoreantiforgeri Token](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtresi, belirli bir eyleme (veya denetleyiciye) yönelik bir antiforgery belirtecinin gereksinimini ortadan kaldırmak için kullanılır. Uygulandığında, bu filtre ve `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` daha yüksek düzeyde (genel olarak veya bir denetleyicide) belirtilen filtreleri geçersiz kılar.

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Kimlik doğrulamasından sonra belirteçleri Yenile

Kullanıcının kimliği doğrulandıktan sonra, Kullanıcı bir görünüm veya Razor sayfalar sayfasına yönlendirildikten sonra belirteçlerin yenilenmesi gerekir.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX ve maça

Geleneksel HTML tabanlı uygulamalarda, antiforgery belirteçleri gizli form alanları kullanılarak sunucuya geçirilir. Modern JavaScript tabanlı uygulamalar ve maça 'Lar, programlama yoluyla birçok istek yapılır. Bu AJAX istekleri, belirteci göndermek için diğer teknikleri (istek üstbilgileri veya tanımlama bilgileri gibi) kullanabilir.

Kimlik bilgileri kimlik doğrulama belirteçlerini depolamak ve sunucudaki API isteklerinin kimliğini doğrulamak için kullanılıyorsa, CSRF olası bir sorundur. Yerel depolama, belirteci depolamak için kullanılıyorsa, yerel depolama alanındaki değerler her istek ile sunucuya otomatik olarak gönderilmediğinden CSRF güvenlik açığı azaltılabilir. Bu nedenle, istemci üzerinde antiforgery belirtecini depolamak ve belirteci istek üst bilgisi olarak göndermek için yerel depolama kullanmak önerilen bir yaklaşımdır.

### <a name="javascript"></a>JavaScript

Görünümler ile JavaScript kullanarak, belirteç, görünümün içinden bir hizmet kullanılarak oluşturulabilir. [Microsoft. AspNetCore. antiforgery. ıantiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) hizmetini görünüme ekleyin ve [Getandstorebelirteçlerini](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)çağırın:

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Bu yaklaşım, sunucudan tanımlama bilgilerini ayarlama veya istemciden okuma konusunda doğrudan anlaşma gereksinimini ortadan kaldırır.

Önceki örnekte, AJAX GÖNDERI üst bilgisinin gizli alan değerini okumak için JavaScript kullanılmaktadır.

JavaScript, tanımlama bilgilerinde belirteçlere de erişebilir ve belirtecin değerine sahip bir üst bilgi oluşturmak için tanımlama bilgisinin içeriğini kullanabilir.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Çağrılan `X-CSRF-TOKEN`bir üst bilgide belirteç göndermek için komut dosyası isteklerinin kabul edilmediği varsayılarak, bir `X-CSRF-TOKEN` üst bilgiyi aramak için antiforgery hizmetini yapılandırın:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Aşağıdaki örnek, uygun üst bilgiyle bir AJAX isteği oluşturmak için JavaScript kullanır:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS, CSRF adresine yönelik bir kural kullanır. Sunucu, adla `XSRF-TOKEN`bir tanımlama bilgisi gönderirse, AngularJS `$http` hizmeti, sunucuya istek gönderdiğinde tanımlama bilgisi değerini bir üstbilgiye ekler. Bu işlem otomatiktir. Üstbilginin istemcide açıkça ayarlanması gerekmez. Üst bilgi adı `X-XSRF-TOKEN`. Sunucunun bu üstbilgiyi algılaması ve içeriğini doğrulaması gerekir.

ASP.NET Core API 'nin uygulama başlangıcında bu kurala göre çalışması için:

* Uygulamanızı adlı `XSRF-TOKEN`bir tanımlama bilgisinde belirteç sunacak şekilde yapılandırın.
* Antiforgery hizmetini adlı `X-XSRF-TOKEN`üstbilgiyi aramak üzere yapılandırın.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Antiforgery 'yi uzat

[Iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) türü, geliştiricilerin her bir belirteçte ek verileri yuvarlak aşağı getirerek CSRF sisteminin davranışını genişletmesini sağlar. [Getaddıtionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) yöntemi, bir alan belirtecinin oluşturulduğu her seferinde çağrılır ve döndürülen değer oluşturulan belirtecin içine gömülür. Bir uygulayıcısı, bir zaman damgası, bir kerelik anahtar veya başka bir değer döndürebilir ve ardından, belirteç doğrulandığında bu verileri doğrulamak için [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) öğesini çağırabilir. İstemcinin Kullanıcı adı, oluşturulan belirteçlere zaten eklenmiş olduğundan, bu bilgileri eklemeniz gerekmez. Bir belirteç ek verileri içeriyorsa ancak `IAntiForgeryAdditionalDataProvider` yapılandırılmamışsa, ek veriler doğrulanmaz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Open Web Application Security projesinde](https://www.owasp.org/index.php/Main_Page) [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) (OWASP).
* <xref:host-and-deploy/web-farm>
