---
title: :::no-loc(Blazor):::Genelleştirme ve yerelleştirme ASP.NET Core
author: guardrex
description: :::no-loc(Razor):::Birden çok kültürde veya dilde kullanıcıların kullanıcılar için erişilebilir hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/globalization-localization
ms.openlocfilehash: f8f261f25d854a9bf36ad3299f4af392d5c4fafd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055887"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="f24e6-103">:::no-loc(Blazor):::Genelleştirme ve yerelleştirme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f24e6-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="f24e6-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="f24e6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f24e6-105">:::no-loc(Razor)::: bileşenlere birden fazla kültürde ve dilde kullanıcılar erişilebilir hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="f24e6-106">Aşağıdaki .NET Genelleştirme ve yerelleştirme senaryoları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="f24e6-107">. NET ' in kaynak sistemi</span><span class="sxs-lookup"><span data-stu-id="f24e6-107">.NET's resources system</span></span>
* <span data-ttu-id="f24e6-108">Kültüre özgü sayı ve Tarih biçimlendirmesi</span><span class="sxs-lookup"><span data-stu-id="f24e6-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="f24e6-109">Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="f24e6-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> uygulamalarında *desteklenir* :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="f24e6-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="f24e6-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolardır ve uygulamalarda **desteklenmez** :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="f24e6-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="f24e6-112">Daha fazla bilgi için bkz. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="f24e6-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="f24e6-113">Genelleştirme</span><span class="sxs-lookup"><span data-stu-id="f24e6-113">Globalization</span></span>

<span data-ttu-id="f24e6-114">:::no-loc(Blazor):::[`@bind`](xref:mvc/views/razor#bind)işlevselliği, kullanıcının geçerli kültürüne göre biçimleri gerçekleştirir ve görüntüleme değerlerini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="f24e6-115">Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="f24e6-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> , aşağıdaki alan türleri için kullanılır ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="f24e6-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="f24e6-117">Yukarıdaki alan türleri:</span><span class="sxs-lookup"><span data-stu-id="f24e6-117">The preceding field types:</span></span>

* <span data-ttu-id="f24e6-118">, Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="f24e6-119">Serbest biçimli metin içeremez.</span><span class="sxs-lookup"><span data-stu-id="f24e6-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="f24e6-120">Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f24e6-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="f24e6-121">Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve şu anda :::no-loc(Blazor)::: tüm büyük tarayıcılarda desteklenmediğinden tarafından desteklenmemektedir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="f24e6-122">[`@bind`](xref:mvc/views/razor#bind) , bir `@bind:culture` <xref:System.Globalization.CultureInfo?displayProperty=fullName> değeri ayrıştırmak ve biçimlendirmek için bir parametresini destekler.</span><span class="sxs-lookup"><span data-stu-id="f24e6-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="f24e6-123">`date`Ve alan türleri kullanılırken bir kültürün belirtilmesi önerilmez `number` .</span><span class="sxs-lookup"><span data-stu-id="f24e6-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="f24e6-124">`date` ve `number` :::no-loc(Blazor)::: gerekli kültürü sağlayan yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="f24e6-125">Localization (Yerelleştirme)</span><span class="sxs-lookup"><span data-stu-id="f24e6-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="f24e6-126">:::no-loc(Blazor WebAssembly)::: uygulamalar, kullanıcının [Dil tercihini](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)kullanarak kültürü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f24e6-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="f24e6-127">Kültürü açıkça yapılandırmak için, <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> ve içinde öğesini <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> ayarlayın `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f24e6-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f24e6-128">Varsayılan olarak, :::no-loc(Blazor WebAssembly)::: Kullanıcı kültürünün tarih ve para birimi gibi değerleri görüntülemesi için gereken en az Genelleştirme kaynaklarını taşır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="f24e6-129">Kültürü dinamik olarak değiştirmeyi desteklemesi gereken uygulamalar `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` Proje dosyasında yapılandırmalıdır:</span><span class="sxs-lookup"><span data-stu-id="f24e6-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="f24e6-130">:::no-loc(Blazor WebAssembly)::: Ayrıca, öğesine geçirilen seçenekler kullanılarak belirli bir uygulama kültürü kullanılarak başlatılacak şekilde yapılandırılabilir `:::no-loc(Blazor):::.start` .</span><span class="sxs-lookup"><span data-stu-id="f24e6-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="f24e6-131">Örneğin, aşağıdaki örnekte kültür kullanılarak başlatılacak şekilde yapılandırılmış bir uygulama gösterilmektedir `en-GB` :</span><span class="sxs-lookup"><span data-stu-id="f24e6-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="f24e6-132">Değeri `applicationCulture` [BCP-47 dil etiketi biçimine](https://tools.ietf.org/html/bcp47)uymalıdır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="f24e6-133">Uygulama yerelleştirmeyi gerektirmiyorsa, uygulamayı kültürü temel alan sabit kültürü destekleyecek şekilde yapılandırabilirsiniz `en-US` :</span><span class="sxs-lookup"><span data-stu-id="f24e6-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f24e6-134">Varsayılan olarak, uygulamalar için ara dil (IL) bağlayıcı yapılandırması, :::no-loc(Blazor WebAssembly)::: açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="f24e6-135">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="f24e6-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="f24e6-136">:::no-loc(Blazor):::Varsayılan olarak seçen kültür çoğu kullanıcı için yeterli olabilir, ancak kullanıcıların tercih ettiği yerel ayarlarını belirtmelerini sağlayan bir yol sunmayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="f24e6-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="f24e6-137">:::no-loc(Blazor WebAssembly):::Kültür seçiciyle örnek bir uygulama için bkz [`LocSample`](https://github.com/pranavkm/LocSample) . yerelleştirme örnek uygulaması.</span><span class="sxs-lookup"><span data-stu-id="f24e6-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="f24e6-138">:::no-loc(Blazor Server)::: uygulamalar, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="f24e6-139">Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.</span><span class="sxs-lookup"><span data-stu-id="f24e6-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="f24e6-140">Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="f24e6-141">[:::no-loc(Cookie):::malar](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="f24e6-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="f24e6-142">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="f24e6-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="f24e6-143">Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization> ..</span><span class="sxs-lookup"><span data-stu-id="f24e6-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="f24e6-144">:::no-loc(Cookie):::malar</span><span class="sxs-lookup"><span data-stu-id="f24e6-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="f24e6-145">Yerelleştirme kültürü :::no-loc(cookie)::: kullanıcının kültürünü kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="f24e6-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="f24e6-146">Yerelleştirme ara yazılımı, :::no-loc(cookie)::: sonraki istekleri kullanıcının kültürünü ayarlamak için okur.</span><span class="sxs-lookup"><span data-stu-id="f24e6-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="f24e6-147">' Nin kullanımı, :::no-loc(cookie)::: WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f24e6-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="f24e6-148">Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="f24e6-149">Bu nedenle, yerelleştirme kültürünün kullanılması :::no-loc(cookie)::: önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="f24e6-150">Kültür bir Yerelleştirmede kalıcı ise kültür atamak için herhangi bir teknik kullanılabilir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="f24e6-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="f24e6-151">Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve :::no-loc(cookie)::: uygulamanın şeması içinde yerelleştirme kültürünü ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f24e6-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="f24e6-152">Aşağıdaki örnek, ' de :::no-loc(cookie)::: Yerelleştirme ara yazılımı tarafından okunabilen geçerli kültürün nasıl ayarlanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="f24e6-153">:::no-loc(Razor)::: `Pages/_Host.cshtml` Dosyada açılış etiketinin hemen içinde bir ifade oluşturun `<body>` :</span><span class="sxs-lookup"><span data-stu-id="f24e6-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="f24e6-154">Yerelleştirme, uygulama tarafından aşağıdaki olay dizisinde işlenir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="f24e6-155">Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="f24e6-156">Kültür, yerelleştirme ara yazılımı tarafından atanır.</span><span class="sxs-lookup"><span data-stu-id="f24e6-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="f24e6-157">:::no-loc(Razor)::: `_Host` Sayfadaki ifade (), `_Host.cshtml` yanıtın bir parçası olarak bir içinde kültürü devam ettirir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="f24e6-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="f24e6-158">Tarayıcı, etkileşimli bir oturum oluşturmak için bir WebSocket bağlantısı açar :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="f24e6-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="f24e6-159">Yerelleştirme ara yazılımı okur :::no-loc(cookie)::: ve kültürü atar.</span><span class="sxs-lookup"><span data-stu-id="f24e6-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="f24e6-160">:::no-loc(Blazor Server):::Oturum doğru kültür ile başlar.</span><span class="sxs-lookup"><span data-stu-id="f24e6-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

<span data-ttu-id="f24e6-161">İle çalışırken <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page> , <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> özelliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f24e6-161">When working with a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>, use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> property:</span></span>

```razor
@{
    this.Context.Response.:::no-loc(Cookie):::s.Append(
        :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
        :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="f24e6-162">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="f24e6-162">Provide UI to choose the culture</span></span>

<span data-ttu-id="f24e6-163">Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-163">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="f24e6-164">Bir Kullanıcı güvenli bir kaynağa erişmeyi denediğinde, işlem Web uygulamasında gerçekleşmelere benzer.</span><span class="sxs-lookup"><span data-stu-id="f24e6-164">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="f24e6-165">Kullanıcı bir oturum açma sayfasına yönlendirilir ve ardından özgün kaynağa yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-165">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="f24e6-166">Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-166">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="f24e6-167">Denetleyici kullanıcının seçili kültürünü bir :::no-loc(cookie)::: olarak ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="f24e6-167">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="f24e6-168">Sunucuda kullanıcının seçili kültürünü ayarlamak :::no-loc(cookie)::: ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda BIR HTTP uç noktası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f24e6-168">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="f24e6-169"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>Açık yeniden yönlendirme saldırılarını engellemek için eylem sonucunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f24e6-169">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="f24e6-170">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="f24e6-170">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="f24e6-171">Uygulama, denetleyici eylemlerini işlemek üzere yapılandırılmamışsa:</span><span class="sxs-lookup"><span data-stu-id="f24e6-171">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="f24e6-172">' Deki hizmet koleksiyonuna MVC hizmetleri ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f24e6-172">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="f24e6-173">Denetleyici uç noktası yönlendirmesi ekleme `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f24e6-173">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="f24e6-174">Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f24e6-174">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f24e6-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f24e6-175">Additional resources</span></span>

* <xref:fundamentals/localization>
