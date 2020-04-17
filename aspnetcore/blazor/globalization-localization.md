---
title: ASP.NET Blazor Çekirdek küreselleşme ve yerelleştirme
author: guardrex
description: Razor bileşenlerini birden çok kültür ve dilde kullanıcılar için nasıl erişilebilir hale getirebileceğimi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1b0db66b23c0caffc6b7c4e4af723c020609612a
ms.sourcegitcommit: d5d45d84fe488427d418de770000f7df44a08370
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81539667"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Blazor Çekirdek küreselleşme ve yerelleştirme

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Jilet bileşenleri, birden fazla kültür ve dilde kullanıcılar tarafından erişilebilir hale getirilebilir. Aşağıdaki .NET küreselleşme ve yerelleştirme senaryoları kullanılabilir:

* . NET'in kaynak sistemi
* Kültüre özel sayı ve tarih biçimlendirme

Sınırlı sayıda ASP.NET Core'un yerelleştirme senaryoları şu anda desteklenir:

* `IStringLocalizer<>`uygulamalarda Blazor *desteklenir.*
* `IHtmlLocalizer<>`, `IViewLocalizer<>`ve Veri Ek Açıklamaları yerelleştirme ASP.NET Core MVC senaryoları ve uygulamalarda Blazor **desteklenmez.**

Daha fazla bilgi için bkz. <xref:fundamentals/localization>.

## <a name="globalization"></a>Genelleştirme

Blazor'ın `@bind` işlevselliği, kullanıcının geçerli kültürüne göre görüntü için biçimleri ve ayrıştırıcı değerleri gerçekleştirir.

Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> tesisten erişilebilir.

[CultureInfo.InvariantKültür](xref:System.Globalization.CultureInfo.InvariantCulture) aşağıdaki alan türleri için`<input type="{TYPE}" />`kullanılır ():

* `date`
* `number`

Önceki alan türleri:

* Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.
* Serbest biçimli metin içeremez.
* Tarayıcının uygulamasına dayalı kullanıcı etkileşimi özellikleri sağlayın.

Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve Blazor tüm büyük tarayıcılar tarafından desteklenmedikleri için şu anda tarafından desteklenmez:

* `datetime-local`
* `month`
* `week`

`@bind`bir `@bind:culture` değerayrıştma <xref:System.Globalization.CultureInfo?displayProperty=fullName> ve biçimlendirme sağlamak için parametreyi destekler. Alan türlerini kullanırken `date` `number` bir kültür belirtilmesi önerilmez. `date`ve `number` gerekli kültürü Blazor sağlayan yerleşik desteğe sahip olmak.

## <a name="localization"></a>Yerelleştirme

### <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly

BlazorWebAssembly uygulamaları, kullanıcının dil [tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.

Kültürü açıkça yapılandırmak için, `CultureInfo.DefaultThreadCurrentCulture` `CultureInfo.DefaultThreadCurrentUICulture` ayarlayın `Program.Main`ve .

Varsayılan olarak, WebAssembly uygulamaları Blazor için ''nin bağlayıcı yapılandırması, Blazoraçıkça istenen yerel durumlar dışında uluslararasılaştırma bilgilerini siler. Bağlayıcının davranışını denetleme hakkında daha fazla bilgi <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>ve kılavuz için bkz.

Varsayılan olarak Blazor seçen kültür çoğu kullanıcı için yeterli olsa da, kullanıcıların tercih ettikleri yerel alanı belirtmeleri için bir yol sunmayı düşünün. Kültür Blazor seçiciiçeren bir WebAssembly örnek uygulaması için [LocSample](https://github.com/pranavkm/LocSample) yerelleştirme örnek uygulamasına bakın.

### <a name="opno-locblazor-server"></a>BlazorSunucu

BlazorSunucu uygulamaları [Yerelleştirme Middleware](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir. Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.

Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:

* [Çerezler](#cookies)
* [Kültürü seçmek için UI sağlayın](#provide-ui-to-choose-the-culture)

Daha fazla bilgi ve <xref:fundamentals/localization>örnekler için bkz.

#### <a name="cookies"></a>Tanımlama bilgileri

Bir yerelleştirme kültür çerezkullanıcının kültürünü devam edebilir. Çerez, uygulamanın `OnGet` ana sayfa *(Pages/Host.cshtml.cs)* yöntemiyle oluşturulur. Yerelleştirme Middleware kullanıcının kültürünü ayarlamak için sonraki isteklerde çerez okur. 

Çerez kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar. Yerelleştirme düzenleri URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışamayabilir, bu nedenle kültürü devam ettiremeyebilir. Bu nedenle, bir yerelleştirme kültür çerez kullanımı önerilen bir yaklaşımdır.

Kültür bir yerelleştirme çerezinde kalıcıysa, herhangi bir teknik bir kültür atamak için kullanılabilir. Uygulamanın sunucu tarafı ASP.NET Core için zaten kurulmuş bir yerelleştirme şeması varsa, uygulamanın varolan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür çerezini ayarlayın.

Aşağıdaki örnek, yerelleştirme middleware tarafından okunabilir bir çerez geçerli kültür ayarlamak için nasıl gösterir. Blazor Server uygulamasında aşağıdaki içerikleri içeren bir *Pages/_Host.cshtml.cs* dosyası oluşturun:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Yerelleştirme, uygulama tarafından aşağıdaki olaylar dizisinde ele alınır:

1. Tarayıcı uygulamaya bir ilk HTTP isteği gönderir.
1. Kültür Yerelleştirme Middleware tarafından atanır.
1. `OnGet` *_Host.cshtml.cs'deki* yöntem, yanıtın bir parçası olarak bir çerezdeki kültürü devam eder.
1. Tarayıcı, etkileşimli Blazor bir Sunucu oturumu oluşturmak için bir WebSocket bağlantısı açar.
1. Yerelleştirme Middleware çerez okur ve kültür atar.
1. Blazor Sunucu oturumu doğru kültürle başlar.

#### <a name="provide-ui-to-choose-the-culture"></a>Kültürü seçmek için UI sağlayın

Kullanıcının bir kültür seçmesine izin verecek kullanıcı kullanıcı nın kullanıcı yı sağlaması için *yeniden yönlendirme tabanlı* bir yaklaşım önerilir. Bu işlem, bir kullanıcı güvenli bir kaynağa erişmeye çalıştığında bir web uygulamasında olana benzer. Kullanıcı oturum açma sayfasına yönlendirilir ve daha sonra özgün kaynağa yönlendirilir. 

Uygulama, bir denetleyiciye yönlendirme yoluyla kullanıcının seçili kültürünü devam ettir. Denetleyici, kullanıcının seçili kültürünü bir çerez olarak ayarlar ve kullanıcıyı orijinal URI'ye yönlendirir.

Kullanıcının seçili kültürünü bir çerezde ayarlamak ve orijinal URI'ye yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP bitiş noktası kurun:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Açık `LocalRedirect` yeniden yönlendirme saldırılarını önlemek için eylem sonucunu kullanın. Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.

Aşağıdaki bileşen, kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirilecekine bir örnek gösterir:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/localization>
