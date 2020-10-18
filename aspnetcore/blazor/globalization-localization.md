---
title: BlazorGenelleştirme ve yerelleştirme ASP.NET Core
author: guardrex
description: RazorBirden çok kültürde veya dilde kullanıcıların kullanıcılar için erişilebilir hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: be73b0c1e33a2cd15c9ff0dc51044f9bd48c43fe
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113822"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>BlazorGenelleştirme ve yerelleştirme ASP.NET Core

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razor bileşenlere birden fazla kültürde ve dilde kullanıcılar erişilebilir hale getirilebilir. Aşağıdaki .NET Genelleştirme ve yerelleştirme senaryoları kullanılabilir:

* . NET ' in kaynak sistemi
* Kültüre özgü sayı ve Tarih biçimlendirmesi

Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> uygulamalarında *desteklenir* Blazor .
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolardır ve uygulamalarda **desteklenmez** Blazor .

Daha fazla bilgi için bkz. <xref:fundamentals/localization>.

## <a name="globalization"></a>Genelleştirme

Blazor[`@bind`](xref:mvc/views/razor#bind)işlevselliği, kullanıcının geçerli kültürüne göre biçimleri gerçekleştirir ve görüntüleme değerlerini ayrıştırır.

Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> , aşağıdaki alan türleri için kullanılır ( `<input type="{TYPE}" />` ):

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

[`@bind`](xref:mvc/views/razor#bind) , bir `@bind:culture` <xref:System.Globalization.CultureInfo?displayProperty=fullName> değeri ayrıştırmak ve biçimlendirmek için bir parametresini destekler. `date`Ve alan türleri kullanılırken bir kültürün belirtilmesi önerilmez `number` . `date` ve `number` Blazor gerekli kültürü sağlayan yerleşik desteğe sahiptir.

## <a name="localization"></a>Localization (Yerelleştirme)

### Blazor WebAssembly

Blazor WebAssembly uygulamalar, kullanıcının [Dil tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.

Kültürü açıkça yapılandırmak için, <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> ve içinde öğesini <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> ayarlayın `Program.Main` .

::: moniker range=">= aspnetcore-5.0"

Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken en az Genelleştirme kaynaklarını taşır. Kültürü dinamik olarak değiştirmeyi desteklemesi gereken uygulamalar `BlazorWebAssemblyLoadAllGlobalizationData` Proje dosyasında yapılandırmalıdır:

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan sabit kültürü destekleyecek şekilde yapılandırabilirsiniz `en-US` :

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Varsayılan olarak, uygulamalar için ara dil (IL) bağlayıcı yapılandırması, Blazor WebAssembly açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır. Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

BlazorVarsayılan olarak seçen kültür çoğu kullanıcı için yeterli olabilir, ancak kullanıcıların tercih ettiği yerel ayarlarını belirtmelerini sağlayan bir yol sunmayı göz önünde bulundurun. Blazor WebAssemblyKültür seçiciyle örnek bir uygulama için bkz [`LocSample`](https://github.com/pranavkm/LocSample) . yerelleştirme örnek uygulaması.

### Blazor Server

Blazor Server uygulamalar, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir. Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.

Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:

* [Cookies](#cookies)
* [Kültürü seçmek için Kullanıcı arabirimi sağlama](#provide-ui-to-choose-the-culture)

Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization> ..

#### <a name="no-loccookies"></a>Cookies

Yerelleştirme kültürü cookie kullanıcının kültürünü kalıcı hale getirebilirler. Yerelleştirme ara yazılımı, cookie sonraki istekleri kullanıcının kültürünü ayarlamak için okur. 

' Nin kullanımı, cookie WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar. Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir. Bu nedenle, yerelleştirme kültürünün kullanılması cookie önerilen yaklaşımdır.

Kültür bir Yerelleştirmede kalıcı ise kültür atamak için herhangi bir teknik kullanılabilir cookie . Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve cookie uygulamanın şeması içinde yerelleştirme kültürünü ayarlayın.

Aşağıdaki örnek, ' de cookie Yerelleştirme ara yazılımı tarafından okunabilen geçerli kültürün nasıl ayarlanacağını gösterir. Razor `Pages/_Host.cshtml` Dosyada açılış etiketinin hemen içinde bir ifade oluşturun `<body>` :

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
1. Razor `_Host` Sayfadaki ifade (), `_Host.cshtml` yanıtın bir parçası olarak bir içinde kültürü devam ettirir cookie .
1. Tarayıcı, etkileşimli bir oturum oluşturmak için bir WebSocket bağlantısı açar Blazor Server .
1. Yerelleştirme ara yazılımı okur cookie ve kültürü atar.
1. Blazor ServerOturum doğru kültür ile başlar.

#### <a name="provide-ui-to-choose-the-culture"></a>Kültürü seçmek için Kullanıcı arabirimi sağlama

Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir. Bir Kullanıcı güvenli bir kaynağa erişmeyi denediğinde, işlem Web uygulamasında gerçekleşmelere benzer. Kullanıcı bir oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir. 

Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir. Denetleyici kullanıcının seçili kültürünü bir cookie olarak ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.

Sunucuda kullanıcının seçili kültürünü ayarlamak cookie ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda BIR HTTP uç noktası oluşturun:

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
