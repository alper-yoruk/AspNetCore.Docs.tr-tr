---
title: Genelleştirme Blazor ve yerelleştirme ASP.NET Core
author: guardrex
description: Birden çok kültürde Razor veya dilde kullanıcıların kullanıcılar için erişilebilir hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772497"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="1d3f3-103">Genelleştirme Blazor ve yerelleştirme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d3f3-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="1d3f3-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="1d3f3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="1d3f3-105">bileşenlere birden fazla kültürde ve dilde kullanıcılar erişilebilir hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-105"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="1d3f3-106">Aşağıdaki .NET Genelleştirme ve yerelleştirme senaryoları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="1d3f3-107">. NET ' in kaynak sistemi</span><span class="sxs-lookup"><span data-stu-id="1d3f3-107">.NET's resources system</span></span>
* <span data-ttu-id="1d3f3-108">Kültüre özgü sayı ve Tarih biçimlendirmesi</span><span class="sxs-lookup"><span data-stu-id="1d3f3-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="1d3f3-109">Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="1d3f3-110">`IStringLocalizer<>`Blazor uygulamalarda *desteklenir* .</span><span class="sxs-lookup"><span data-stu-id="1d3f3-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="1d3f3-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`ve veri ek açıklamaları YERELLEŞTIRME ASP.NET Core MVC senaryolardır ve **not supported** Blazor uygulamalarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="1d3f3-112">Daha fazla bilgi için bkz. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="1d3f3-113">Genelleştirme</span><span class="sxs-lookup"><span data-stu-id="1d3f3-113">Globalization</span></span>

Blazor<span data-ttu-id="1d3f3-114">`@bind` işlevselliği, kullanıcının geçerli kültürüne göre biçimleri gerçekleştirir ve görüntüleme değerlerini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="1d3f3-115">Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="1d3f3-116">Aşağıdaki alan türleri için [CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) kullanılır (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="1d3f3-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="1d3f3-117">Yukarıdaki alan türleri:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-117">The preceding field types:</span></span>

* <span data-ttu-id="1d3f3-118">, Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="1d3f3-119">Serbest biçimli metin içeremez.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="1d3f3-120">Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="1d3f3-121">Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve şu anda tüm büyük tarayıcılarda Blazor desteklenmediğinden tarafından desteklenmemektedir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="1d3f3-122">`@bind`, `@bind:culture` bir değeri ayrıştırmak ve biçimlendirmek <xref:System.Globalization.CultureInfo?displayProperty=fullName> için bir parametresini destekler.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="1d3f3-123">`date` Ve `number` alan türleri kullanılırken bir kültürün belirtilmesi önerilmez.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="1d3f3-124">`date`ve `number` gerekli kültürü sağlayan yerleşik Blazor desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="1d3f3-125">Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="1d3f3-125">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="1d3f3-126">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1d3f3-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="1d3f3-127">WebAssembly uygulamaları, kullanıcının [Dil tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="1d3f3-128">Kültürü açıkça yapılandırmak için, ve `CultureInfo.DefaultThreadCurrentCulture` `CultureInfo.DefaultThreadCurrentUICulture` içinde `Program.Main`öğesini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="1d3f3-129">Varsayılan olarak, Blazorwebassembly uygulamaları Blazor için bağlayıcı yapılandırması, açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini şeritleri.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="1d3f3-130">Bağlayıcının davranışını denetleme hakkında daha fazla bilgi ve yönergeler için bkz <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>..</span><span class="sxs-lookup"><span data-stu-id="1d3f3-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="1d3f3-131">Varsayılan olarak seçen kültür Blazor çoğu kullanıcı için yeterli olabilir, ancak kullanıcıların tercih ettiği yerel ayarlarını belirtmelerini sağlayan bir yol sunmayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="1d3f3-132">Kültür seçiciyle Blazor webassembly örnek uygulaması için bkz. [locsample](https://github.com/pranavkm/LocSample) yerelleştirme örnek uygulaması.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="1d3f3-133">Server</span><span class="sxs-lookup"><span data-stu-id="1d3f3-133"> Server</span></span>

Blazor<span data-ttu-id="1d3f3-134">Sunucu uygulamaları, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="1d3f3-135">Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="1d3f3-136">Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="1d3f3-137">Çerezler</span><span class="sxs-lookup"><span data-stu-id="1d3f3-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="1d3f3-138">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="1d3f3-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="1d3f3-139">Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization>..</span><span class="sxs-lookup"><span data-stu-id="1d3f3-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="1d3f3-140">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="1d3f3-140">Cookies</span></span>

<span data-ttu-id="1d3f3-141">Yerelleştirme kültürü tanımlama bilgisi kullanıcının kültürünü kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="1d3f3-142">Tanımlama bilgisi, uygulamanın ana bilgisayar `OnGet` sayfası (*Pages/Host. cshtml. cs*) yöntemi tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="1d3f3-143">Yerelleştirme ara yazılımı, sonraki isteklerde Kullanıcı kültürünü ayarlamak için tanımlama bilgilerini okur.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="1d3f3-144">Tanımlama bilgisinin kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="1d3f3-145">Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="1d3f3-146">Bu nedenle, yerelleştirme kültürü tanımlama bilgisinin kullanılması önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="1d3f3-147">Kültür bir yerelleştirme tanımlama bilgisinde kalıcı hale getirilir kültür atamak için herhangi bir teknik kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="1d3f3-148">Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür tanımlama bilgisini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="1d3f3-149">Aşağıdaki örnekte, yerelleştirme ara yazılımı tarafından okunabilen bir tanımlama bilgisinde geçerli kültürün nasıl ayarlanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="1d3f3-150">Sunucu uygulamasında aşağıdaki içeriklerle bir *Pages/_Host. cshtml. cs* dosyası oluşturun: Blazor</span><span class="sxs-lookup"><span data-stu-id="1d3f3-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="1d3f3-151">Yerelleştirme, uygulama tarafından aşağıdaki olay dizisinde işlenir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="1d3f3-152">Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="1d3f3-153">Kültür, yerelleştirme ara yazılımı tarafından atanır.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="1d3f3-154">`OnGet` *_Host. cshtml. cs* içindeki yöntemi, yanıtın bir parçası olarak bir tanımlama bilgisinde kültürü devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="1d3f3-155">Tarayıcı, etkileşimli Blazor bir sunucu oturumu oluşturmak Için bir WebSocket bağlantısı açar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="1d3f3-156">Yerelleştirme ara yazılımı tanımlama bilgisini okur ve kültürü atar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="1d3f3-157">Blazor Sunucu oturumu doğru kültür ile başlar.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="1d3f3-158">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="1d3f3-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="1d3f3-159">Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="1d3f3-160">Bir Kullanıcı güvenli bir kaynağa erişmeyi denediğinde, işlem Web uygulamasında gerçekleşmelere benzer.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="1d3f3-161">Kullanıcı bir oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="1d3f3-162">Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="1d3f3-163">Denetleyici kullanıcının seçili kültürünü bir tanımlama bilgisine ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="1d3f3-164">Bir tanımlama bilgisinde kullanıcının seçili kültürünü ayarlamak ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP uç noktası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="1d3f3-165">Açık yeniden `LocalRedirect` yönlendirme saldırılarını engellemek için eylem sonucunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="1d3f3-166">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="1d3f3-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="1d3f3-167">Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="1d3f3-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="1d3f3-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1d3f3-168">Additional resources</span></span>

* <xref:fundamentals/localization>
