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
ms.openlocfilehash: 4345dd8525c2e72aaddc8e45a4fd4d9bfdd63040
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326523"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="d9a0e-103">BlazorGenelleştirme ve yerelleştirme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9a0e-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="d9a0e-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="d9a0e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d9a0e-105">Razor bileşenlere birden fazla kültürde ve dilde kullanıcılar erişilebilir hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="d9a0e-106">Aşağıdaki .NET Genelleştirme ve yerelleştirme senaryoları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="d9a0e-107">. NET ' in kaynak sistemi</span><span class="sxs-lookup"><span data-stu-id="d9a0e-107">.NET's resources system</span></span>
* <span data-ttu-id="d9a0e-108">Kültüre özgü sayı ve Tarih biçimlendirmesi</span><span class="sxs-lookup"><span data-stu-id="d9a0e-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="d9a0e-109">Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="d9a0e-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> uygulamalarında *desteklenir* Blazor .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="d9a0e-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolardır ve uygulamalarda **desteklenmez** Blazor .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="d9a0e-112">Daha fazla bilgi için bkz. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="d9a0e-113">Genelleştirme</span><span class="sxs-lookup"><span data-stu-id="d9a0e-113">Globalization</span></span>

<span data-ttu-id="d9a0e-114">Blazor[`@bind`](xref:mvc/views/razor#bind)işlevselliği, kullanıcının geçerli kültürüne göre biçimleri gerçekleştirir ve görüntüleme değerlerini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="d9a0e-115">Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="d9a0e-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> , aşağıdaki alan türleri için kullanılır ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="d9a0e-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="d9a0e-117">Yukarıdaki alan türleri:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-117">The preceding field types:</span></span>

* <span data-ttu-id="d9a0e-118">, Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="d9a0e-119">Serbest biçimli metin içeremez.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="d9a0e-120">Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="d9a0e-121">Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve şu anda Blazor tüm büyük tarayıcılarda desteklenmediğinden tarafından desteklenmemektedir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="d9a0e-122">[`@bind`](xref:mvc/views/razor#bind) , bir `@bind:culture` <xref:System.Globalization.CultureInfo?displayProperty=fullName> değeri ayrıştırmak ve biçimlendirmek için bir parametresini destekler.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="d9a0e-123">`date`Ve alan türleri kullanılırken bir kültürün belirtilmesi önerilmez `number` .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="d9a0e-124">`date` ve `number` Blazor gerekli kültürü sağlayan yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="d9a0e-125">Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="d9a0e-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="d9a0e-126">Blazor WebAssembly uygulamalar, kullanıcının [Dil tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="d9a0e-127">Kültürü açıkça yapılandırmak için, <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> ve içinde öğesini <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> ayarlayın `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d9a0e-128">Varsayılan olarak, Blazor WebAssembly Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken en az Genelleştirme kaynaklarını taşır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-128">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="d9a0e-129">Kültürü dinamik olarak değiştirmeyi desteklemesi gereken uygulamalar `BlazorWebAssemblyLoadAllGlobalizationData` Proje dosyasında yapılandırmalıdır:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-129">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="d9a0e-130">Blazor WebAssembly Ayrıca, öğesine geçirilen seçenekler kullanılarak belirli bir uygulama kültürü kullanılarak başlatılacak şekilde yapılandırılabilir `Blazor.start` .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-130">Blazor WebAssembly can also be configured to launch using a specific application culture using options passed to `Blazor.start`.</span></span> <span data-ttu-id="d9a0e-131">Örneğin, aşağıdaki örnekte kültür kullanılarak başlatılacak şekilde yapılandırılmış bir uygulama gösterilmektedir `en-GB` :</span><span class="sxs-lookup"><span data-stu-id="d9a0e-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="d9a0e-132">Değeri `applicationCulture` [BCP-47 dil etiketi biçimine](https://tools.ietf.org/html/bcp47)uymalıdır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="d9a0e-133">Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan sabit kültürü destekleyecek şekilde yapılandırabilirsiniz `en-US` :</span><span class="sxs-lookup"><span data-stu-id="d9a0e-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d9a0e-134">Varsayılan olarak, uygulamalar için ara dil (IL) bağlayıcı yapılandırması, Blazor WebAssembly açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-134">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="d9a0e-135">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="d9a0e-136">BlazorVarsayılan olarak seçen kültür çoğu kullanıcı için yeterli olabilir, ancak kullanıcıların tercih ettiği yerel ayarlarını belirtmelerini sağlayan bir yol sunmayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-136">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="d9a0e-137">Blazor WebAssemblyKültür seçiciyle örnek bir uygulama için bkz [`LocSample`](https://github.com/pranavkm/LocSample) . yerelleştirme örnek uygulaması.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-137">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="d9a0e-138">Blazor Server uygulamalar, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-138">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="d9a0e-139">Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="d9a0e-140">Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="d9a0e-141">[Cookies](#cookies)</span><span class="sxs-lookup"><span data-stu-id="d9a0e-141">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="d9a0e-142">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="d9a0e-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="d9a0e-143">Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization> ..</span><span class="sxs-lookup"><span data-stu-id="d9a0e-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="d9a0e-144">Cookies</span><span class="sxs-lookup"><span data-stu-id="d9a0e-144">Cookies</span></span>

<span data-ttu-id="d9a0e-145">Yerelleştirme kültürü cookie kullanıcının kültürünü kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-145">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="d9a0e-146">Yerelleştirme ara yazılımı, cookie sonraki istekleri kullanıcının kültürünü ayarlamak için okur.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-146">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="d9a0e-147">' Nin kullanımı, cookie WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-147">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="d9a0e-148">Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="d9a0e-149">Bu nedenle, yerelleştirme kültürünün kullanılması cookie önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-149">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="d9a0e-150">Kültür bir Yerelleştirmede kalıcı ise kültür atamak için herhangi bir teknik kullanılabilir cookie .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-150">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="d9a0e-151">Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve cookie uygulamanın şeması içinde yerelleştirme kültürünü ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="d9a0e-152">Aşağıdaki örnek, ' de cookie Yerelleştirme ara yazılımı tarafından okunabilen geçerli kültürün nasıl ayarlanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-152">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="d9a0e-153">Razor `Pages/_Host.cshtml` Dosyada açılış etiketinin hemen içinde bir ifade oluşturun `<body>` :</span><span class="sxs-lookup"><span data-stu-id="d9a0e-153">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

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

<span data-ttu-id="d9a0e-154">Yerelleştirme, uygulama tarafından aşağıdaki olay dizisinde işlenir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="d9a0e-155">Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="d9a0e-156">Kültür, yerelleştirme ara yazılımı tarafından atanır.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="d9a0e-157">Razor `_Host` Sayfadaki ifade (), `_Host.cshtml` yanıtın bir parçası olarak bir içinde kültürü devam ettirir cookie .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-157">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="d9a0e-158">Tarayıcı, etkileşimli bir oturum oluşturmak için bir WebSocket bağlantısı açar Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="d9a0e-158">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="d9a0e-159">Yerelleştirme ara yazılımı okur cookie ve kültürü atar.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-159">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="d9a0e-160">Blazor ServerOturum doğru kültür ile başlar.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-160">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="d9a0e-161">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="d9a0e-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="d9a0e-162">Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="d9a0e-163">Bir Kullanıcı güvenli bir kaynağa erişmeyi denediğinde, işlem Web uygulamasında gerçekleşmelere benzer.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="d9a0e-164">Kullanıcı bir oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="d9a0e-165">Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="d9a0e-166">Denetleyici kullanıcının seçili kültürünü bir cookie olarak ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-166">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="d9a0e-167">Sunucuda kullanıcının seçili kültürünü ayarlamak cookie ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda BIR HTTP uç noktası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-167">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="d9a0e-168"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>Açık yeniden yönlendirme saldırılarını engellemek için eylem sonucunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="d9a0e-169">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="d9a0e-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="d9a0e-170">Uygulama, denetleyici eylemlerini işlemek üzere yapılandırılmamışsa:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="d9a0e-171">' Deki hizmet koleksiyonuna MVC hizmetleri ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d9a0e-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="d9a0e-172">Denetleyici uç noktası yönlendirmesi ekleme `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d9a0e-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="d9a0e-173">Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d9a0e-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d9a0e-174">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d9a0e-174">Additional resources</span></span>

* <xref:fundamentals/localization>
