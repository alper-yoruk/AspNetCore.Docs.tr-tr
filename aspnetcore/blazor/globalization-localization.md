---
title: BlazorGenelleştirme ve yerelleştirme ASP.NET Core
author: guardrex
description: RazorBirden çok kültürde veya dilde kullanıcıların kullanıcılar için erişilebilir hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 42b61c9af0c1809ecb7d9a45ec8edfa815e2df22
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102327"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>BlazorGenelleştirme ve yerelleştirme ASP.NET Core

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razorbileşenlere birden fazla kültürde ve dilde kullanıcılar erişilebilir hale getirilebilir. Aşağıdaki .NET Genelleştirme ve yerelleştirme senaryoları kullanılabilir:

* . NET ' in kaynak sistemi
* Kültüre özgü sayı ve Tarih biçimlendirmesi

Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer>ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> uygulamalarında *desteklenir* Blazor .
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolardır ve uygulamalarda **desteklenmez** Blazor .

Daha fazla bilgi için bkz. <xref:fundamentals/localization>.

## <a name="globalization"></a>Genelleştirme

Blazor[`@bind`](xref:mvc/views/razor#bind)işlevselliği, kullanıcının geçerli kültürüne göre biçimleri gerçekleştirir ve görüntüleme değerlerini ayrıştırır.

Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>, aşağıdaki alan türleri için kullanılır ( `<input type="{TYPE}" />` ):

* `date`
* `number`

Yukarıdaki alan türleri:

* , Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.
* Serbest biçimli metin içeremez.
* Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.

Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve şu anda Blazor tüm büyük tarayıcılarda desteklenmediğinden tarafından desteklenmemektedir:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind), bir `@bind:culture` <xref:System.Globalization.CultureInfo?displayProperty=fullName> değeri ayrıştırmak ve biçimlendirmek için bir parametresini destekler. `date`Ve alan türleri kullanılırken bir kültürün belirtilmesi önerilmez `number` . `date`ve `number` Blazor gerekli kültürü sağlayan yerleşik desteğe sahiptir.

## <a name="localization"></a>Yerelleştirme

### <a name="blazor-webassembly"></a>BlazorWebAssembly

BlazorWebAssembly uygulamaları, kullanıcının [Dil tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.

Kültürü açıkça yapılandırmak için, <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> ve içinde öğesini <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> ayarlayın `Program.Main` .

Varsayılan olarak, Blazor Blazor webassembly uygulamaları için bağlayıcı yapılandırması, açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini şeritleri. Bağlayıcının davranışını denetleme hakkında daha fazla bilgi ve yönergeler için bkz <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization> ..

BlazorVarsayılan olarak seçen kültür çoğu kullanıcı için yeterli olabilir, ancak kullanıcıların tercih ettiği yerel ayarlarını belirtmelerini sağlayan bir yol sunmayı göz önünde bulundurun. BlazorKültür seçiciyle webassembly örnek uygulaması için bkz. [locsample](https://github.com/pranavkm/LocSample) yerelleştirme örnek uygulaması.

### <a name="blazor-server"></a>BlazorServer

BlazorSunucu uygulamaları, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir. Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.

Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:

* [Çerezler](#cookies)
* [Kültürü seçmek için Kullanıcı arabirimi sağlama](#provide-ui-to-choose-the-culture)

Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization> ..

#### <a name="cookies"></a>Tanımlama bilgileri

Yerelleştirme kültürü tanımlama bilgisi kullanıcının kültürünü kalıcı hale getirebilirler. Yerelleştirme ara yazılımı, sonraki isteklerde Kullanıcı kültürünü ayarlamak için tanımlama bilgilerini okur. 

Tanımlama bilgisinin kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar. Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir. Bu nedenle, yerelleştirme kültürü tanımlama bilgisinin kullanılması önerilen yaklaşımdır.

Kültür bir yerelleştirme tanımlama bilgisinde kalıcı hale getirilir kültür atamak için herhangi bir teknik kullanılabilir. Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür tanımlama bilgisini ayarlayın.

Aşağıdaki örnekte, yerelleştirme ara yazılımı tarafından okunabilen bir tanımlama bilgisinde geçerli kültürün nasıl ayarlanacağı gösterilmektedir. Razor *Pages/_Host. cshtml* dosyasında açılış etiketinin hemen içinde bir ifade oluşturun `<body>` :

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

Yerelleştirme, uygulama tarafından aşağıdaki olay dizisinde işlenir:

1. Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.
1. Kültür, yerelleştirme ara yazılımı tarafından atanır.
1. Razor `_Host` Sayfadaki ifade (*_Host. cshtml*), yanıtın bir parçası olarak bir tanımlama bilgisinde kültürü devam ettirir.
1. Tarayıcı, etkileşimli bir sunucu oturumu oluşturmak için bir WebSocket bağlantısı açar Blazor .
1. Yerelleştirme ara yazılımı tanımlama bilgisini okur ve kültürü atar.
1. BlazorSunucu oturumu doğru kültür ile başlar.

#### <a name="provide-ui-to-choose-the-culture"></a>Kültürü seçmek için Kullanıcı arabirimi sağlama

Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir. Bir Kullanıcı güvenli bir kaynağa erişmeyi denediğinde, işlem Web uygulamasında gerçekleşmelere benzer. Kullanıcı bir oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir. 

Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir. Denetleyici kullanıcının seçili kültürünü bir tanımlama bilgisine ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.

Bir tanımlama bilgisinde kullanıcının seçili kültürünü ayarlamak ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP uç noktası oluşturun:

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
> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>Açık yeniden yönlendirme saldırılarını engellemek için eylem sonucunu kullanın. Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.

Uygulama, denetleyici eylemlerini işlemek üzere yapılandırılmamışsa:

* ' Deki hizmet koleksiyonuna MVC hizmetleri ekleyin `Startup.ConfigureServices` :

  ```csharp
  services.AddControllers();
  ```

* Denetleyici uç noktası yönlendirmesi ekleme `Startup.Configure` :

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:

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
