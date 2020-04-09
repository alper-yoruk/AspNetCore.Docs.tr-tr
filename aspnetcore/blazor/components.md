---
title: Core Razor bileşenleri ASP.NET oluşturma ve kullanma
author: guardrex
description: Verilere nasıl bağlanılacağı, olayları nasıl işleyeceğiniz ve bileşen yaşam döngülerini nasıl yönetleyeceğiniz de dahil olmak üzere Razor bileşenlerini nasıl oluşturup kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: bc1d07aef9cd60b89343a034168daa6754f4421b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306504"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="74beb-103">Core Razor bileşenleri ASP.NET oluşturma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="74beb-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="74beb-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="74beb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="74beb-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="74beb-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="74beb-106">uygulamalar *bileşenleri*kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="74beb-106"> apps are built using *components*.</span></span> <span data-ttu-id="74beb-107">Bileşen, sayfa, iletişim kutusu veya form gibi bağımsız bir kullanıcı arabirimi (UI) bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="74beb-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="74beb-108">Bileşen, HTML biçimlendirmesini ve veri enjekte etmek veya UI olaylarına yanıt vermek için gereken işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="74beb-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="74beb-109">Bileşenler esnek ve hafiftir.</span><span class="sxs-lookup"><span data-stu-id="74beb-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="74beb-110">Bunlar iç içe, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="74beb-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="74beb-111">Component classes</span></span>

<span data-ttu-id="74beb-112">Bileşenler, C# ve HTML biçimlendirmesinin bir kombinasyonu kullanılarak [Razor](xref:mvc/views/razor) bileşen dosyalarında *(.jilet)* uygulanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="74beb-113">Bir bileşen Blazor resmen bir Razor *bileşeni*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="74beb-114">Bir bileşenin adı büyük harfli bir karakterle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="74beb-115">Örneğin, *MyCoolComponent.razor* geçerlidir ve *myCoolComponent.razor* geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="74beb-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="74beb-116">Bir bileşenin ui'si HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="74beb-117">Dinamik işleme mantığı (örneğin, döngüler, koşullular, ifadeler) [Razor](xref:mvc/views/razor)adlı katıştırılmış c# sözdizimi kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="74beb-118">Bir uygulama derlendiğinde, HTML biçimlendirmeve C# oluşturma mantığı bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="74beb-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="74beb-119">Oluşturulan sınıfın adı dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="74beb-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="74beb-120">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="74beb-121">Blokta, `@code` bileşen durumu (özellikler, alanlar) olay işleme veya diğer bileşen mantığını tanımlamak için yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="74beb-122">Birden `@code` fazla blokca izin verilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="74beb-123">Bileşen üyeleri, `@`'ile başlayan C# ifadeleri kullanılarak bileşenin oluşturma mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="74beb-124">Örneğin, bir C# alanı alan adına `@` öne doğru edilerek işlenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="74beb-125">Aşağıdaki örnek, şunları değerlendirir ve işler:</span><span class="sxs-lookup"><span data-stu-id="74beb-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="74beb-126">`_headingFontStyle`için CSS özellik `font-style`değerine.</span><span class="sxs-lookup"><span data-stu-id="74beb-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="74beb-127">`_headingText`öğenin içeriğine. `<h1>`</span><span class="sxs-lookup"><span data-stu-id="74beb-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="74beb-128">Bileşen başlangıçta işlendikten sonra, bileşen olaylara yanıt olarak işağacını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="74beb-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="74beb-129">sonra yeni işleme ağacını öncekiyle karşılaştırır ve tarayıcının Belge Nesnesi Modeli'nde (DOM) herhangi bir değişiklik uygular.</span><span class="sxs-lookup"><span data-stu-id="74beb-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="74beb-130">Bileşenler sıradan C# sınıflarıdır ve proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="74beb-131">Web sayfaları üreten bileşenler genellikle *Sayfalar* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="74beb-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="74beb-132">Sayfa dışı bileşenler sık sık *Paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="74beb-133">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve bileşenin uygulama içindeki konumundan (klasör) türetilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="74beb-134">Uygulamanın kök ad alanı `BlazorApp` ise `Counter` ve bileşen *Sayfalar* klasöründe bulunursa:</span><span class="sxs-lookup"><span data-stu-id="74beb-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="74beb-135">Bileşenin `Counter` ad `BlazorApp.Pages`alanı.</span><span class="sxs-lookup"><span data-stu-id="74beb-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="74beb-136">Bileşenin tam nitelikli tür `BlazorApp.Pages.Counter`adı.</span><span class="sxs-lookup"><span data-stu-id="74beb-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="74beb-137">Daha fazla bilgi [için, İçe Aktar bileşenleri](#import-components) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74beb-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="74beb-138">Özel bir klasör kullanmak için, özel klasörün ad alanını ana bileşene veya uygulamanın *_Imports.razor* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="74beb-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="74beb-139">Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı `BlazorApp`olduğunda *Bileşenler* klasöründeki bileşenleri kullanılabilir hale getirir:</span><span class="sxs-lookup"><span data-stu-id="74beb-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="74beb-140">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="74beb-140">Static assets</span></span>

Blazor<span data-ttu-id="74beb-141">projenin [web kökü (wwwroot) klasörüne](xref:fundamentals/index#web-root)statik varlıkları yerleştiren ASP.NET Core uygulamaları kuralını izler.</span><span class="sxs-lookup"><span data-stu-id="74beb-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="74beb-142">Statik bir varlık için`/`web köküne başvurmak için temel göreli bir yol ( ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="74beb-143">Aşağıdaki örnekte, *logo.png* fiziksel olarak *{PROJECT ROOT}/wwwroot/images* klasöründe yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="74beb-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="74beb-144">Jilet bileşenleri tilde-slash gösterimi`~/` **desteklemez** ( ).</span><span class="sxs-lookup"><span data-stu-id="74beb-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="74beb-145">Bir uygulamanın temel yolunu ayarlama hakkında <xref:host-and-deploy/blazor/index#app-base-path>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="74beb-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="74beb-146">Tag Yardımcıları bileşenlerde desteklenmez</span><span class="sxs-lookup"><span data-stu-id="74beb-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="74beb-147">[Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) Razor bileşenlerinde *(.jilet* dosyaları) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="74beb-148">Tag Helper benzeri işlevselliği Blazorsağlamak için, Tag Helper ile aynı işlevsellik teshisine sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="74beb-149">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="74beb-149">Use components</span></span>

<span data-ttu-id="74beb-150">Bileşenler, HTML öğesi sözdizimini kullanarak bunları beyan ederek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="74beb-151">Bir bileşeni kullanmanın biçimlendirmesi, etiketin adının bileşen türü olduğu bir HTML etiketine benzer.</span><span class="sxs-lookup"><span data-stu-id="74beb-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="74beb-152">*Index.razor'daki* aşağıdaki biçimlendirme `HeadingComponent` bir örneği işler:</span><span class="sxs-lookup"><span data-stu-id="74beb-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="74beb-153">*Bileşenler/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="74beb-154">Bir bileşen, bir bileşen adı eşleşmeyen bir büyük harfe sahip bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu belirten bir uyarı yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="74beb-155">Bileşenin `@using` ad alanı için bir yönerge eklemek bileşeni kullanılabilir hale getirir ve bu da uyarıyı çözer.</span><span class="sxs-lookup"><span data-stu-id="74beb-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="74beb-156">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="74beb-156">Routing</span></span>

<span data-ttu-id="74beb-157">Yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlayarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="74beb-158">Yönergesi olan `@page` bir Razor dosyası derlendiğinde, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> oluşturulan sınıfa rota şablonu belirten bir verilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="74beb-159">Çalışma zamanında, yönlendirici a `RouteAttribute` ile bileşen sınıfları arar ve hangi bileşen istenen URL eşleşen bir rota şablonu vardır işler.</span><span class="sxs-lookup"><span data-stu-id="74beb-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="74beb-160">Daha fazla bilgi için bkz. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="74beb-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="74beb-161">Parametreler</span><span class="sxs-lookup"><span data-stu-id="74beb-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="74beb-162">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="74beb-162">Route parameters</span></span>

<span data-ttu-id="74beb-163">Bileşenler, yönergede sağlanan rota şablonundan rota parametrelerini `@page` alabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="74beb-164">Yönlendirici, ilgili bileşen parametrelerini doldurmak için rota parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="74beb-165">*Sayfalar/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="74beb-166">İsteğe bağlı parametreler desteklenmez, bu nedenle önceki örnekte iki `@page` yönerge uygulanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="74beb-167">İlk bir parametre olmadan bileşene navigasyon izin verir.</span><span class="sxs-lookup"><span data-stu-id="74beb-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="74beb-168">İkinci `@page` yönerge `{text}` rota parametresini alır ve `Text` değeri özelliğe atar.</span><span class="sxs-lookup"><span data-stu-id="74beb-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="74beb-169">Birden çok klasör sınırları`*`/`**`boyunca yolu yakalayan *catch-all* parametre sözdizimi (*.razor)* ile **desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="74beb-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="74beb-170">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="74beb-170">Component parameters</span></span>

<span data-ttu-id="74beb-171">Bileşenler, öznitelik ile bileşen sınıfında ortak özellikleri kullanılarak `[Parameter]` tanımlanan *bileşen parametreleri*olabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="74beb-172">Biçimlendirmede bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="74beb-173">*Bileşenler/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="74beb-174">Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` `ChildComponent`'nin özelliğinin değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="74beb-175">*Sayfalar/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="74beb-176">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="74beb-176">Child content</span></span>

<span data-ttu-id="74beb-177">Bileşenler başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-177">Components can set the content of another component.</span></span> <span data-ttu-id="74beb-178">Atama bileşeni, alıcı bileşeni belirten etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-178">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="74beb-179">Aşağıdaki örnekte, `ChildComponent` işlenebilmek için `RenderFragment`UI'nin bir kesimini temsil eden bir `ChildContent` özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="74beb-179">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="74beb-180">`ChildContent` Değer, içeriğin işlenmesi gereken bileşenin biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-180">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="74beb-181">Değeri `ChildContent` üst bileşenden alınır ve Bootstrap panelin içinde `panel-body`işlenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-181">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="74beb-182">*Bileşenler/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="74beb-183">İçeriği alan özelliğin `RenderFragment` adı `ChildContent` sözleşmeyle adlandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-183">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="74beb-184">Örnek `ParentComponent` uygulamadaki içerik `ChildComponent` `<ChildComponent>` etiketlerin içine yerleştirerek işlemek için içerik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-184">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="74beb-185">*Sayfalar/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-185">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="74beb-186">Öznitelik ve rasgele parametreler</span><span class="sxs-lookup"><span data-stu-id="74beb-186">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="74beb-187">Bileşenler, bileşenin bildirilen parametrelerine ek olarak ek öznitelikleri yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-187">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="74beb-188">Ek öznitelikler bir sözlükte yakalanabilir ve bileşen [`@attributes`](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir öğeye *splatted.*</span><span class="sxs-lookup"><span data-stu-id="74beb-188">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="74beb-189">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir biçimlendirme öğesi üreten bir bileşeni tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-189">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="74beb-190">Örneğin, birçok parametreyi destekleyen bir öznitelikleri `<input>` ayrı ayrı tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-190">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="74beb-191">Aşağıdaki örnekte, birinci `<input>` öğe`id="useIndividualParams"`( ) tek tek `<input>` bileşen`id="useAttributesDict"`parametrelerini kullanırken, ikinci öğe ( ) öznitelik splatting kullanır:</span><span class="sxs-lookup"><span data-stu-id="74beb-191">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="74beb-192">Parametre türü dize `IEnumerable<KeyValuePair<string, object>>` tuşları ile uygulanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-192">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="74beb-193">Kullanmak `IReadOnlyDictionary<string, object>` da bu senaryoda bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="74beb-193">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="74beb-194">Her iki `<input>` yaklaşımı kullanarak işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="74beb-194">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="74beb-195">Rasgele öznitelikleri kabul etmek için, `[Parameter]` `CaptureUnmatchedValues` `true`özellik kümesi ile öznitelik kullanarak bir bileşen parametresi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="74beb-195">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="74beb-196">Üzerindeki `CaptureUnmatchedValues` `[Parameter]` özellik, parametrenin diğer parametreyle eşleşmeyecek tüm öznitelikleri eşleştirmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-196">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="74beb-197">Bir bileşen yalnızca `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="74beb-197">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="74beb-198">Kullanılan `CaptureUnmatchedValues` özellik türü dize tuşları `Dictionary<string, object>` ile atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-198">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="74beb-199">`IEnumerable<KeyValuePair<string, object>>`veya `IReadOnlyDictionary<string, object>` bu senaryoda seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="74beb-199">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="74beb-200">Öğe özniteliklerinin `@attributes` konumuna göre konum önemlidir.</span><span class="sxs-lookup"><span data-stu-id="74beb-200">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="74beb-201">Öğeye splatted zaman, `@attributes` öznitelikleri sağdan sola (sondan birinciye) işlenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-201">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="74beb-202">Bir `Child` bileşeni tüketen bir bileşenin aşağıdaki örneğini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="74beb-202">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="74beb-203">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-203">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="74beb-204">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-204">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="74beb-205">Bileşenin `Child` özniteliği' nin `extra` sağında `@attributes`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-205">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="74beb-206">Öznitelikler `Parent` sağdan `<div>` sola (sondan birinciye) işlendiğinden, bileşenin işlenmesi ek öznitelikten `extra="5"` geçirildiğinde içerir:</span><span class="sxs-lookup"><span data-stu-id="74beb-206">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="74beb-207">`extra` Aşağıdaki örnekte, `@attributes` `Child` bileşenin `<div>`sırası ve tersine çevrilmesi:</span><span class="sxs-lookup"><span data-stu-id="74beb-207">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="74beb-208">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="74beb-209">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-209">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="74beb-210">Ek öznitelik geçirildiğinde `extra="10"` bileşende işlenen `<div>` içerir: `Parent`</span><span class="sxs-lookup"><span data-stu-id="74beb-210">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="74beb-211">Bileşenlere yapılan başvuruları yakalama</span><span class="sxs-lookup"><span data-stu-id="74beb-211">Capture references to components</span></span>

<span data-ttu-id="74beb-212">Bileşen başvuruları, söz konusu örne komutlar verebilmeniz için bileşen `Show` örneğine başvurmanın bir yolunu `Reset`sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-212">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="74beb-213">Bileşen başvurularını yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="74beb-213">To capture a component reference:</span></span>

* <span data-ttu-id="74beb-214">Alt [`@ref`](xref:mvc/views/razor#ref) bileşene bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="74beb-214">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="74beb-215">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="74beb-215">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="74beb-216">Bileşen işlendiğinde, `_loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="74beb-216">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="74beb-217">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74beb-217">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="74beb-218">`_loginDialog` Değişken yalnızca bileşen işlendikten sonra doldurulur ve `MyLoginDialog` çıktısı öğeyi içerir.</span><span class="sxs-lookup"><span data-stu-id="74beb-218">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="74beb-219">O ana kadar atıfta bulunulacak bir şey yok.</span><span class="sxs-lookup"><span data-stu-id="74beb-219">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="74beb-220">Bileşen işleme bittikten sonra bileşen başvurularını işlemek için [OnAfterRenderAsync veya OnAfterRender yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-220">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="74beb-221">Bir döngüdeki bileşenlere başvurmak [#13358 için](https://github.com/dotnet/aspnetcore/issues/13358)bkz.</span><span class="sxs-lookup"><span data-stu-id="74beb-221">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="74beb-222">Bileşen başvurularını yakalama öğesi başvuruları [yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanırken, bu bir JavaScript interop özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="74beb-222">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="74beb-223">Bileşen başvuruları yalnızca .NET kodunda kullanılan JavaScript koduna&mdash;geçirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-223">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="74beb-224">Alt **not** bileşenlerin durumunu mutasyona uğratmak için bileşen referansları kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="74beb-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="74beb-225">Bunun yerine, alt bileşenlere veri aktarmak için normal bildirim parametrelerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="74beb-226">Normal bildirim parametrelerinin kullanılması, doğru zamanlarda otomatik olarak yeniden işlenen alt bileşenlerle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-226">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="74beb-227">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağırma</span><span class="sxs-lookup"><span data-stu-id="74beb-227">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="74beb-228">yürütme `SynchronizationContext` tek bir mantıksal iş parçacığı zorlamak için a kullanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-228"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="74beb-229">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/lifecycle) ve tarafından Blazor yükseltilen tüm `SynchronizationContext`olay geri aramaları bu şekilde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="74beb-229">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="74beb-230">Bir bileşenin zamanlayıcı veya diğer bildirimler gibi harici bir olaya göre güncellenmesi `InvokeAsync` gerektiğinde, Blazor''nin `SynchronizationContext`''sine ' gönderilecek yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-230">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="74beb-231">Örneğin, güncelleştirilmiş durum herhangi bir dinleme bileşeni bildirebilir bir *noter hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="74beb-231">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="74beb-232">Tekli `NotifierService` olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="74beb-232">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="74beb-233">WebAssembly'de, Blazor hizmeti `Program.Main`şu şekilde kaydettirin:</span><span class="sxs-lookup"><span data-stu-id="74beb-233">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="74beb-234">Sunucu'da, Blazor hizmeti `Startup.ConfigureServices`şu şekilde kaydettirin:</span><span class="sxs-lookup"><span data-stu-id="74beb-234">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="74beb-235">Bir `NotifierService` bileşeni güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="74beb-235">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="74beb-236">`NotifierService` Önceki örnekte, `OnNotify` bileşenin yöntemi Blazor'nin `SynchronizationContext`dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="74beb-236">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="74beb-237">`InvokeAsync`doğru içeriğe geçmek ve bir işleme sıraya girmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-237">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="74beb-238">Elementlerin ve bileşenlerin korunmasını kontrol etmek için anahtarı kullanın \@</span><span class="sxs-lookup"><span data-stu-id="74beb-238">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="74beb-239">Öğelerin veya bileşenlerin ve öğelerin veya bileşenlerin Blazorbir listesini işlerken, 'ın disme algoritması önceki öğelerden veya bileşenlerden hangilerinin tutulabildiğine ve model nesnelerinin bunlara nasıl eşleneceklerine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="74beb-239">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="74beb-240">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="74beb-240">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="74beb-241">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="74beb-241">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="74beb-242">Koleksiyonun `People` içeriği eklenen, silinen veya yeniden sipariş edilen girişlerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-242">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="74beb-243">Bileşen yeniden işlendiğinde, `<DetailsEditor>` bileşen farklı `Details` parametre değerleri alacak şekilde değişebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-243">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="74beb-244">Bu beklenenden daha karmaşık yeniden işleme neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-244">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="74beb-245">Bazı durumlarda, yeniden oluşturma, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-245">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="74beb-246">Eşleme `@key` işlemi, yönerge özniteliği ile denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-246">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="74beb-247">`@key`difüzyon algoritmasının anahtarın değerine göre öğelerin veya bileşenlerin korunmasını garanti etmesi neden olur:</span><span class="sxs-lookup"><span data-stu-id="74beb-247">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="74beb-248">Koleksiyon değiştiğinde, difüzör aletme `<DetailsEditor>` algoritması `person` örnekler ve örnekler arasındaki ilişkiyi korur: `People`</span><span class="sxs-lookup"><span data-stu-id="74beb-248">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="74beb-249">Listeden `Person` `People` a silinirse, ui'den yalnızca karşılık gelen `<DetailsEditor>` örnek kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-249">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="74beb-250">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-250">Other instances are left unchanged.</span></span>
* <span data-ttu-id="74beb-251">Listedeki `Person` bazı konuma a eklenirse, bu `<DetailsEditor>` ilgili konuma yeni bir örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-251">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="74beb-252">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="74beb-253">`Person` Girişler yeniden sıralanırsa, ilgili `<DetailsEditor>` örnekler korunur ve UI'de yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-253">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="74beb-254">Bazı senaryolarda, yeniden `@key` işlemenin karmaşıklığını en aza indirir ve odak konumu gibi DOM'un durum lu bölümleriyle ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="74beb-254">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="74beb-255">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="74beb-255">Keys are local to each container element or component.</span></span> <span data-ttu-id="74beb-256">Anahtarlar belge genelinde genel olarak karşılaştırılmıyor.</span><span class="sxs-lookup"><span data-stu-id="74beb-256">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="74beb-257">Anahtar ne \@zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="74beb-257">When to use \@key</span></span>

<span data-ttu-id="74beb-258">Tipik olarak, bir liste `@key` işlendiğinde (örneğin, bir `@foreach` blokta) ve uygun bir değer tanımlamak `@key`için var olduğunda kullanmak mantıklı.</span><span class="sxs-lookup"><span data-stu-id="74beb-258">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="74beb-259">Bir nesne `@key` değiştiğinde Blazor bir öğeyi veya bileşen alt ağacını korumayı önlemek için de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="74beb-259">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="74beb-260">Değişiklik `@currentPerson` olursa, `@key` öznitelik Blazor yönergesi `<div>` tüm ünü ve soyundan gelenleri atmaya ve ui içindeki alt ağacı yeni öğeler ve bileşenlerle yeniden oluşturmaya zorlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-260">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="74beb-261">Bu, değişiklik olduğunda `@currentPerson` hiçbir UI durumunun korunduğunu garanti etmek gerekirse yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-261">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="74beb-262">Anahtar kullanılmadığında \@</span><span class="sxs-lookup"><span data-stu-id="74beb-262">When not to use \@key</span></span>

<span data-ttu-id="74beb-263">'ile difüzör yaparken `@key`bir performans maliyeti vardır.</span><span class="sxs-lookup"><span data-stu-id="74beb-263">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="74beb-264">Performans maliyeti büyük değildir, ancak `@key` yalnızca öğeyi veya bileşen koruma kurallarının denetlenerek uygulamaya yarar sağolup olmadığını belirtin.</span><span class="sxs-lookup"><span data-stu-id="74beb-264">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="74beb-265">`@key` Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="74beb-265">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="74beb-266">Kullanmanın `@key` tek avantajı, eşlemeyi seçen difüzör algoritması yerine, model örneklerinin korunan bileşen örneklerine *nasıl* eşlendirileceği üzerinde denetim yapmaktır.</span><span class="sxs-lookup"><span data-stu-id="74beb-266">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="74beb-267">Anahtar için \@kullanılacak değerler</span><span class="sxs-lookup"><span data-stu-id="74beb-267">What values to use for \@key</span></span>

<span data-ttu-id="74beb-268">Genel olarak, aşağıdaki değer türlerinden birini sağlamak `@key`mantıklıdır:</span><span class="sxs-lookup"><span data-stu-id="74beb-268">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="74beb-269">Örnek nesne örnekleri (örneğin, `Person` önceki örnekte olduğu gibi bir örnek).</span><span class="sxs-lookup"><span data-stu-id="74beb-269">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="74beb-270">Bu, nesne başvuru eşitliğine dayalı koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-270">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="74beb-271">Benzersiz tanımlayıcılar (örneğin, tür `int`, `string`veya `Guid`birincil anahtar değerleri).</span><span class="sxs-lookup"><span data-stu-id="74beb-271">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="74beb-272">Kullanılan `@key` değerlerin çakışmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="74beb-272">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="74beb-273">Çakışan değerler aynı üst öğe Blazor içinde algılanırsa, eski öğeleri veya bileşenleri yeni öğelerveya bileşenlerle deterministically eşleyemediği için bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="74beb-273">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="74beb-274">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerler kullanın.</span><span class="sxs-lookup"><span data-stu-id="74beb-274">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="74beb-275">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="74beb-275">Partial class support</span></span>

<span data-ttu-id="74beb-276">Jilet bileşenleri kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="74beb-276">Razor components are generated as partial classes.</span></span> <span data-ttu-id="74beb-277">Jilet bileşenleri aşağıdaki yaklaşımlardan biri kullanılarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="74beb-277">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="74beb-278">C# kodu, tek [`@code`](xref:mvc/views/razor#code) bir dosyada HTML biçimlendirmesi ve Razor kodu içeren bir blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-278">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="74beb-279">şablonlar bu yaklaşımı kullanarak Razor bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-279"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="74beb-280">C# kodu kısmi sınıf olarak tanımlanan kod arkası dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-280">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="74beb-281">Aşağıdaki örnekte, `Counter` Blazor şablondan `@code` oluşturulan bir uygulamada blok bulunan varsayılan bileşen gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="74beb-281">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="74beb-282">HTML biçimlendirmesi, Razor kodu ve C# kodu aynı dosyadadır:</span><span class="sxs-lookup"><span data-stu-id="74beb-282">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="74beb-283">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-283">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="74beb-284">Bileşen, `Counter` kısmi sınıfa sahip bir kod arkası dosya kullanılarak da oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="74beb-284">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="74beb-285">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="74beb-285">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="74beb-286">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="74beb-286">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="74beb-287">Gerekli olan tüm ad alanlarını gerektiği gibi kısmi sınıf dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="74beb-287">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="74beb-288">Razor bileşenleri tarafından kullanılan tipik ad alanları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="74beb-288">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="74beb-289">Taban sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="74beb-289">Specify a base class</span></span>

<span data-ttu-id="74beb-290">Yönerge, [`@inherits`](xref:mvc/views/razor#inherits) bir bileşen için taban sınıf belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-290">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="74beb-291">Aşağıdaki örnek, bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir taban sınıfı `BlazorRocksBase`nasıl devralabileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="74beb-291">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="74beb-292">Taban sınıf' tan `ComponentBase`türemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="74beb-292">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="74beb-293">*Sayfalar/BlazorRocks.jilet*:</span><span class="sxs-lookup"><span data-stu-id="74beb-293">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="74beb-294">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="74beb-294">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="74beb-295">Bir öznitelik belirtin</span><span class="sxs-lookup"><span data-stu-id="74beb-295">Specify an attribute</span></span>

<span data-ttu-id="74beb-296">Öznitelikler, yönerge ile [`@attribute`](xref:mvc/views/razor#attribute) Razor bileşenlerinde belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-296">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="74beb-297">Aşağıdaki örnek bileşen `[Authorize]` sınıfına öznitelik uygular:</span><span class="sxs-lookup"><span data-stu-id="74beb-297">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="74beb-298">Bileşenleri içe aktarma</span><span class="sxs-lookup"><span data-stu-id="74beb-298">Import components</span></span>

<span data-ttu-id="74beb-299">Razor ile yazılmış bir bileşenin ad alanı temel alınarak (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="74beb-299">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="74beb-300">[`@namespace`](xref:mvc/views/razor#namespace)(*.razor*) biçimlendirme`@namespace BlazorSample.MyNamespace`( ).</span><span class="sxs-lookup"><span data-stu-id="74beb-300">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="74beb-301">Proje proje `RootNamespace` dosyasında (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="74beb-301">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="74beb-302">Proje dosyasının dosya adından *(.csproj)* ve proje kökünden bileşene giden yol alınan proje adı.</span><span class="sxs-lookup"><span data-stu-id="74beb-302">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="74beb-303">Örneğin, çerçeve *{PROJECT ROOT}/Pages/Index.razor* *(BlazorSample.csproj)* ad alanına `BlazorSample.Pages`giderir.</span><span class="sxs-lookup"><span data-stu-id="74beb-303">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="74beb-304">Bileşenler C# adı bağlama kurallarına uyar.</span><span class="sxs-lookup"><span data-stu-id="74beb-304">Components follow C# name binding rules.</span></span> <span data-ttu-id="74beb-305">Bu `Index` örnekteki bileşen için, kapsamdaki bileşenlerin tümü bileşenlerişunlardır:</span><span class="sxs-lookup"><span data-stu-id="74beb-305">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="74beb-306">Aynı klasörde, *Sayfalar*.</span><span class="sxs-lookup"><span data-stu-id="74beb-306">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="74beb-307">Projenin kökünde, açıkça farklı bir ad alanı belirtmeden bileşenler.</span><span class="sxs-lookup"><span data-stu-id="74beb-307">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="74beb-308">Farklı bir ad alanında tanımlanan bileşenler Razor [`@using`](xref:mvc/views/razor#using) yönergesi kullanılarak kapsam içine getirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-308">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="74beb-309">*BlazorSample/Shared/* klasöründe başka bir bileşen `Index.razor` `@using` `NavMenu.razor`varsa, bileşen aşağıdaki ifadeyle kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="74beb-309">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="74beb-310">Bileşenler, [`@using`](xref:mvc/views/razor#using) yönerge gerektirmeyen tam nitelikli adlarını kullanarak da başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="74beb-310">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="74beb-311">Yeterlilik `global::` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="74beb-311">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="74beb-312">Diğer adlı `using` deyimlerle bileşenleri alma `@using Foo = Bar`(örneğin,) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-312">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="74beb-313">Kısmen nitelikli adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-313">Partially qualified names aren't supported.</span></span> <span data-ttu-id="74beb-314">Örneğin, ekleme `@using BlazorSample` ve başvurma `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-314">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="74beb-315">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="74beb-315">Conditional HTML element attributes</span></span>

<span data-ttu-id="74beb-316">HTML öğesi öznitelikleri .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-316">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="74beb-317">Değer `false` veya, `null`öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-317">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="74beb-318">Değer ise, `true`öznitelik en aza indirgenmiş hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-318">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="74beb-319">Aşağıdaki örnekte, `IsCompleted` öğenin `checked` biçimlendirmesinde işlenmeyi belirler:</span><span class="sxs-lookup"><span data-stu-id="74beb-319">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="74beb-320">`true`Ise, `IsCompleted` onay kutusu aşağıdaki gibi işlenir:</span><span class="sxs-lookup"><span data-stu-id="74beb-320">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="74beb-321">`false`Ise, `IsCompleted` onay kutusu aşağıdaki gibi işlenir:</span><span class="sxs-lookup"><span data-stu-id="74beb-321">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="74beb-322">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="74beb-322">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="74beb-323">[Arya basıldığında](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)ki bazı HTML öznitelikleri ,.NET türü . `bool`</span><span class="sxs-lookup"><span data-stu-id="74beb-323">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="74beb-324">Bu gibi durumlarda, `string` `bool`bir . yerine bir tür kullanın</span><span class="sxs-lookup"><span data-stu-id="74beb-324">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="74beb-325">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="74beb-325">Raw HTML</span></span>

<span data-ttu-id="74beb-326">Dizeleri normalde DOM metin düğümleri kullanılarak işlenir, bu da içerebilecekleri herhangi bir biçimlendirmenin göz ardı edildiği ve gerçek metin olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="74beb-326">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="74beb-327">Ham HTML işlemek için HTML içeriğini `MarkupString` bir değere sarın.</span><span class="sxs-lookup"><span data-stu-id="74beb-327">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="74beb-328">Değer HTML veya SVG olarak ayrıştırılır ve DOM'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="74beb-328">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="74beb-329">Herhangi bir güvenilmeyen kaynaktan oluşturulmuş ham HTML oluşturma bir **güvenlik riskidir** ve kaçınılmalıdır!</span><span class="sxs-lookup"><span data-stu-id="74beb-329">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="74beb-330">Aşağıdaki örnek, bir `MarkupString` bileşenin işlenen çıktısına statik HTML içeriği bloğu eklemek için türü kullanır:</span><span class="sxs-lookup"><span data-stu-id="74beb-330">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="74beb-331">Basamaklı değerler ve parametreler</span><span class="sxs-lookup"><span data-stu-id="74beb-331">Cascading values and parameters</span></span>

<span data-ttu-id="74beb-332">Bazı senaryolarda, bileşen [parametrelerini](#component-parameters)kullanarak bir ata bileşeninden soyundan gelen bir bileşene veri akışı sakıncalıdır , özellikle de birkaç bileşen katmanı varsa.</span><span class="sxs-lookup"><span data-stu-id="74beb-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="74beb-333">Basamaklı değerler ve parametreler, bir ata bileşeninin tüm soyundan geçen bileşenlere değer sağlaması için uygun bir yol sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="74beb-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="74beb-334">Basamaklı değerler ve parametreler de bileşenlerin koordine etmesi için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="74beb-335">Tema örneği</span><span class="sxs-lookup"><span data-stu-id="74beb-335">Theme example</span></span>

<span data-ttu-id="74beb-336">Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıf, bileşen hiyerarşisinden akacak tema bilgilerini, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaşmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-336">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="74beb-337">*UIThemeClasses/ ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="74beb-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="74beb-338">Bir ata bileşeni Basamaklı Değer bileşenini kullanarak basamaklı bir değer sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="74beb-339">Bileşen, `CascadingValue` bileşen hiyerarşisinin bir alt ağacını sarar ve bu alt ağaçtaki tüm bileşenlere tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-339">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="74beb-340">Örneğin, örnek uygulama,`ThemeInfo` `@Body` uygulamanın düzenlerinden birinde tema bilgilerini , özelliğin düzen gövdesini oluşturan tüm bileşenler için basamaklı bir parametre olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="74beb-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="74beb-341">`ButtonClass`düzen `btn-success` bileşeninde bir değer atanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-341">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="74beb-342">Herhangi bir azalan bileşen basamaklı nesne `ThemeInfo` aracılığıyla bu özelliği tüketebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="74beb-343">`CascadingValuesParametersLayout`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="74beb-343">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="74beb-344">Basamaklı değerlerden yararlanmak için bileşenler, özniteliği kullanarak `[CascadingParameter]` basamaklı parametreleri bildirir.</span><span class="sxs-lookup"><span data-stu-id="74beb-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="74beb-345">Basamaklı değerler, türe göre basamaklı parametrelere bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-345">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="74beb-346">Örnek uygulamada, `CascadingValuesParametersTheme` bileşen basamaklı değeri `ThemeInfo` basamaklı bir parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="74beb-346">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="74beb-347">Parametre, bileşen tarafından görüntülenen düğmelerden biri için CSS sınıfını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="74beb-347">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="74beb-348">`CascadingValuesParametersTheme`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="74beb-348">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="74beb-349">Aynı alt ağaç içinde aynı türde birden çok `Name` değer basamaklı `CascadingValue` için, `CascadingParameter`her bileşen ve karşılık gelen benzersiz bir dize sağlayın.</span><span class="sxs-lookup"><span data-stu-id="74beb-349">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="74beb-350">Aşağıdaki örnekte, `CascadingValue` iki bileşen ada `MyCascadingType` göre farklı örnekleri basamaklı:</span><span class="sxs-lookup"><span data-stu-id="74beb-350">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="74beb-351">Bir soyundan gelen bileşende, basamaklı parametreler değerlerini ata bileşenindeki ilgili basamaklı değerlerden ada göre alır:</span><span class="sxs-lookup"><span data-stu-id="74beb-351">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="74beb-352">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="74beb-352">TabSet example</span></span>

<span data-ttu-id="74beb-353">Basamaklı parametreler, bileşenlerin bileşen hiyerarşisi içinde işbirliği yapmasına da olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="74beb-353">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="74beb-354">Örneğin, örnek uygulamada aşağıdaki *TabSet* örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="74beb-354">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="74beb-355">Örnek uygulamaseks `ITab` uygulamak bir arayüze sahiptir:</span><span class="sxs-lookup"><span data-stu-id="74beb-355">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="74beb-356">Bileşen, `CascadingValuesParametersTabSet` birkaç `TabSet` `Tab` bileşen içeren bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="74beb-356">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="74beb-357">Alt `Tab` bileşenler açıkça parametre olarak `TabSet`geçirilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-357">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="74beb-358">Bunun yerine, `Tab` alt bileşenler alt içeriğinin `TabSet`bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="74beb-358">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="74beb-359">Ancak, `TabSet` üstbilgi ve etkin `Tab` sekmeyi işleyebilir, böylece yine de her bileşen hakkında bilmek gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek `TabSet` için, bileşen kendisini daha `Tab` sonra soyundan gelen bileşenler tarafından alınan basamaklı bir değer olarak *sağlayabilir.*</span><span class="sxs-lookup"><span data-stu-id="74beb-359">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="74beb-360">`TabSet`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="74beb-360">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="74beb-361">Soyundan gelen `Tab` bileşenler, `TabSet` içereni basamaklı bir parametre `Tab` olarak yakalar, `TabSet` böylece bileşenler kendilerini sekmenin etkin olduğu koordinata ekler ve koordine eder.</span><span class="sxs-lookup"><span data-stu-id="74beb-361">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="74beb-362">`Tab`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="74beb-362">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="74beb-363">Jilet şablonları</span><span class="sxs-lookup"><span data-stu-id="74beb-363">Razor templates</span></span>

<span data-ttu-id="74beb-364">Render parçaları Razor şablonsözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-364">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="74beb-365">Jilet şablonları, bir UI snippet tanımlamanın ve aşağıdaki biçimi varsaymanın bir yoludur:</span><span class="sxs-lookup"><span data-stu-id="74beb-365">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="74beb-366">Aşağıdaki örnekte, şablonların `RenderFragment` `RenderFragment<T>` doğrudan bir bileşende nasıl belirtilen ve değerlere verilebildiğini ve oluşturulacak şekilde gösterildiği gösterilebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-366">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="74beb-367">Render parçaları da [şablonbileşenlerine](xref:blazor/templated-components)bağımsız değişken olarak geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="74beb-367">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="74beb-368">Önceki kodun işlenme çıktısı:</span><span class="sxs-lookup"><span data-stu-id="74beb-368">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="74beb-369">Ölçeklenebilir Vektör Grafikleri (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="74beb-369">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="74beb-370">HTML'yi Blazor işlediğinden, Ölçeklenebilir Vektör Grafikleri (SVG) görüntüleri *(.svg)* dahil olmak `<img>` üzere tarayıcı destekli görüntüler etiket üzerinden desteklenir:</span><span class="sxs-lookup"><span data-stu-id="74beb-370">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="74beb-371">Benzer şekilde, SVG görüntüleri bir stylesheet dosyasının CSS kurallarında desteklenir (*.css):*</span><span class="sxs-lookup"><span data-stu-id="74beb-371">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="74beb-372">Ancak, satır satırlı SVG biçimlendirmesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-372">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="74beb-373">Bir `<svg>` etiketi doğrudan bileşen dosyasına *(.jilet)* yerleştirirseniz, temel görüntü oluşturma desteklenir, ancak birçok gelişmiş senaryo henüz desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="74beb-373">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="74beb-374">Örneğin, `<use>` etiketlere şu anda saygı `@bind` duyulmuyor ve bazı SVG etiketleri ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="74beb-374">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="74beb-375">Bu sınırlamaları ileride piyasaya sürülecek şekilde ele almayı bekliyoruz.</span><span class="sxs-lookup"><span data-stu-id="74beb-375">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74beb-376">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="74beb-376">Additional resources</span></span>

* <span data-ttu-id="74beb-377"><xref:security/blazor/server>&ndash; Kaynak tükenmesi ile uğraşmak gerekir Sunucu uygulamaları oluşturma Blazor kılavuzu içerir.</span><span class="sxs-lookup"><span data-stu-id="74beb-377"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
