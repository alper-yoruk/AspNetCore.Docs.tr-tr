---
title: ASP.NET Core Razor bileşenleri oluşturma ve kullanma
author: guardrex
description: Verileri bağlama, olayları işleme ve Razor bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: de8696488292dfc409c5ed83ea0b88a54a001696
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003064"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="263b9-103">ASP.NET Core Razor bileşenleri oluşturma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="263b9-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="263b9-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="263b9-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="263b9-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="263b9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="263b9-106">uygulamalar, *Bileşenler*kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="263b9-106"> apps are built using *components*.</span></span> <span data-ttu-id="263b9-107">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="263b9-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="263b9-108">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="263b9-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="263b9-109">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="263b9-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="263b9-110">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="263b9-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="263b9-111">Component classes</span></span>

<span data-ttu-id="263b9-112">Bileşenler, C# ve [Razor](xref:mvc/views/razor) HTML biçimlendirme birleşimi kullanılarak bileşen dosyalarında (*. Razor*) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="263b9-113">İçindeki Blazor bir bileşen bir \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="263b9-114">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="263b9-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="263b9-115">Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="263b9-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="263b9-116">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="263b9-117">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı [Razor](xref:mvc/views/razor)gömülü C# sözdizimi kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="263b9-118">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="263b9-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="263b9-119">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="263b9-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="263b9-120">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="263b9-121">`@code` Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="263b9-122">Birden çok `@code` blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="263b9-123">Bileşen üyeleri, ile `@`başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="263b9-124">Örneğin, bir C# alanı alan adının önüne eklenerek `@` işlenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="263b9-125">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="263b9-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="263b9-126">`headingFontStyle`için CSS özellik değerine `font-style`.</span><span class="sxs-lookup"><span data-stu-id="263b9-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="263b9-127">`headingText``<h1>` öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="263b9-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="263b9-128">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="263b9-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="263b9-129">ardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.</span><span class="sxs-lookup"><span data-stu-id="263b9-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="263b9-130">Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="263b9-131">Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="263b9-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="263b9-132">Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="263b9-133">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="263b9-134">Uygulamanın kök ad alanı ise `BlazorApp` ve `Counter` bileşen *Sayfalar* klasöründe bulunuyorsa:</span><span class="sxs-lookup"><span data-stu-id="263b9-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="263b9-135">`Counter` Bileşenin ad alanı `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="263b9-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="263b9-136">Bileşenin tam nitelikli tür adı `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="263b9-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="263b9-137">Daha fazla bilgi için [bileşenleri Içeri aktarma](#import-components) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="263b9-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="263b9-138">Özel bir klasör kullanmak için, özel klasörün ad alanını üst bileşene ya da uygulamanın *_Imports. Razor* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="263b9-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="263b9-139">Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı olduğunda bir *Bileşenler* klasöründeki bileşenleri kullanılabilir yapar `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="263b9-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="263b9-140">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="263b9-140">Static assets</span></span>

Blazor<span data-ttu-id="263b9-141">Projenin [Web kökü (Wwwroot) klasörü](xref:fundamentals/index#web-root)altında statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.</span><span class="sxs-lookup"><span data-stu-id="263b9-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="263b9-142">Statik bir varlık için Web köküne başvurmak`/`üzere temel göreli bir yol () kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="263b9-143">Aşağıdaki örnekte, *logo. png* fiziksel olarak *{Project root}/Wwwroot/Images* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="263b9-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="263b9-144">bileşenler, tilde işareti gösterimini (`~/` **) desteklemez.**</span><span class="sxs-lookup"><span data-stu-id="263b9-144"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="263b9-145">Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:host-and-deploy/blazor/index#app-base-path>..</span><span class="sxs-lookup"><span data-stu-id="263b9-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="263b9-146">Etiket Yardımcıları bileşenlerde desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="263b9-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="263b9-147">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , Razor bileşenlerinde (*. Razor* dosyaları) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="263b9-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="263b9-148">' De Blazoretiket Yardımcısı benzeri işlevsellik sağlamak Için, etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="263b9-149">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="263b9-149">Use components</span></span>

<span data-ttu-id="263b9-150">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="263b9-151">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="263b9-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="263b9-152">*Index. Razor* dosyasında aşağıdaki biçimlendirme bir `HeadingComponent` örneği işler:</span><span class="sxs-lookup"><span data-stu-id="263b9-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="263b9-153">*Bileşenler/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="263b9-154">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="263b9-155">Bileşenin ad `@using` alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="263b9-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="263b9-156">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="263b9-156">Routing</span></span>

<span data-ttu-id="263b9-157">Uygulamasında Blazor yönlendirme, uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="263b9-158">Yönergeyle Razor bir dosya derlendiğinde, oluşturulan sınıfa yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> değer verilir. `@page`</span><span class="sxs-lookup"><span data-stu-id="263b9-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="263b9-159">Çalışma zamanında, yönlendirici bileşen sınıflarını bir `RouteAttribute` ile arar ve hangi BILEŞENIN istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="263b9-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="263b9-160">Daha fazla bilgi için bkz. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="263b9-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="263b9-161">Parametreler</span><span class="sxs-lookup"><span data-stu-id="263b9-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="263b9-162">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="263b9-162">Route parameters</span></span>

<span data-ttu-id="263b9-163">Bileşenler, `@page` yönergede belirtilen yol şablonundan rota parametreleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="263b9-164">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="263b9-165">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="263b9-166">İsteğe bağlı parametreler desteklenmez, bu nedenle `@page` önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="263b9-167">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="263b9-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="263b9-168">İkinci `@page` yönerge, `{text}` Route parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="263b9-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="263b9-169">Birden çok klasör sınırlarındaki Razor yolu`*`/`**`yakalayan *catch-all* parametresi sözdizimi (), bileşenlerde (*. Razor* **) desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="263b9-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="263b9-170">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="263b9-170">Component parameters</span></span>

<span data-ttu-id="263b9-171">Bileşenler, bileşen sınıfında `[Parameter]` özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="263b9-172">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="263b9-173">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="263b9-174">Örnek uygulamadan aşağıdaki örnekte, öğesinin `ParentComponent` `Title` özelliğinin değerini ayarlar. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="263b9-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="263b9-175">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="263b9-176">Kendi *bileşen parametrelerine*yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="263b9-177">Daha fazla bilgi için [kendi parametre özelliklerine yazan bileşenleri oluşturma](#dont-create-components-that-write-to-their-own-parameter-properties) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="263b9-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="263b9-178">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="263b9-178">Child content</span></span>

<span data-ttu-id="263b9-179">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-179">Components can set the content of another component.</span></span> <span data-ttu-id="263b9-180">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="263b9-181">Aşağıdaki örnekte, öğesinin işlemek için `ChildComponent` bir Kullanıcı `ChildContent` arabirimi segmentini temsil `RenderFragment`eden öğesini temsil eden bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="263b9-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="263b9-182">Değeri `ChildContent` , bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="263b9-183">Değeri `ChildContent` , ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="263b9-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="263b9-184">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="263b9-185">`RenderFragment` İçeriği alan özelliğin kural tarafından adlandırılması `ChildContent` gerekir.</span><span class="sxs-lookup"><span data-stu-id="263b9-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="263b9-186">Örnek `ParentComponent` uygulamadaki ' de içeriği `ChildComponent` `<ChildComponent>` etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="263b9-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="263b9-187">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="263b9-188">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="263b9-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="263b9-189">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="263b9-190">Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen [`@attributes`](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* .</span><span class="sxs-lookup"><span data-stu-id="263b9-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="263b9-191">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="263b9-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="263b9-192">Örneğin, çok sayıda parametreyi destekleyen bir `<input>` için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="263b9-193">Aşağıdaki örnekte `<input>` , ilk öğesi (`id="useIndividualParams"`) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe (`id="useAttributesDict"`) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="263b9-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="263b9-194">Parametrenin türü dize anahtarlarıyla gerçekleştirmelidir `IEnumerable<KeyValuePair<string, object>>` .</span><span class="sxs-lookup"><span data-stu-id="263b9-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="263b9-195">Bu `IReadOnlyDictionary<string, object>` senaryoda ayrıca bir seçenek de vardır.</span><span class="sxs-lookup"><span data-stu-id="263b9-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="263b9-196">Her iki `<input>` yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="263b9-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="263b9-197">Rastgele öznitelikleri kabul etmek için `[Parameter]` `CaptureUnmatchedValues` özelliği olarak `true`ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="263b9-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="263b9-198">Üzerindeki `CaptureUnmatchedValues` `[Parameter]` özelliği, parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="263b9-199">Bir bileşen yalnızca ile `CaptureUnmatchedValues`tek bir parametre tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="263b9-200">İle `CaptureUnmatchedValues` kullanılan özellik türü dize anahtarlarıyla atanabilir `Dictionary<string, object>` olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="263b9-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="263b9-201">`IEnumerable<KeyValuePair<string, object>>`Ayrıca `IReadOnlyDictionary<string, object>` , Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="263b9-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="263b9-202">Öğe özniteliklerinin konumuna `@attributes` göreli konumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="263b9-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="263b9-203">Öğe `@attributes` üzerinde ne zaman bırakıldığında, öznitelikler sağdan sola (son-ilk) işlenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="263b9-204">Bir `Child` bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="263b9-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="263b9-205">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="263b9-206">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="263b9-207">`Child` Bileşenin `extra` özniteliği öğesinin `@attributes`sağına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="263b9-208">Öznitelikler `Parent` sağdan sola ( `<div>` en `extra="5"` son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:</span><span class="sxs-lookup"><span data-stu-id="263b9-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="263b9-209">`extra` Aşağıdaki örnekte, `@attributes` ve sırası `Child` bileşen tarafından `<div>`tersine çevrilir:</span><span class="sxs-lookup"><span data-stu-id="263b9-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="263b9-210">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="263b9-211">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="263b9-212">Bileşendeki işlenen `<div>` ek öznitelik ile geçirildiğinde şunları içerir `extra="10"` `Parent`</span><span class="sxs-lookup"><span data-stu-id="263b9-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="263b9-213">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="263b9-213">Capture references to components</span></span>

<span data-ttu-id="263b9-214">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya `Show` `Reset`gibi komutları bu örneğe verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="263b9-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="263b9-215">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="263b9-215">To capture a component reference:</span></span>

* <span data-ttu-id="263b9-216">Alt bileşene [`@ref`](xref:mvc/views/razor#ref) bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="263b9-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="263b9-217">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="263b9-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="263b9-218">Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="263b9-218">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="263b9-219">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="263b9-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="263b9-220">`loginDialog` Değişken yalnızca bileşen işlendikten sonra ve çıktısı `MyLoginDialog` öğesi içerdiğinde doldurulur.</span><span class="sxs-lookup"><span data-stu-id="263b9-220">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="263b9-221">Bu noktaya kadar başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="263b9-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="263b9-222">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için [Onafterrenderasync veya OnAfterRender yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="263b9-223">Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="263b9-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="263b9-224">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="263b9-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="263b9-225">Bileşen başvuruları yalnızca .NET kodunda kullanıldıkları JavaScript&mdash;koduna aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="263b9-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="263b9-226">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="263b9-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="263b9-227">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="263b9-228">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="263b9-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="263b9-229">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="263b9-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="263b9-230">yürütmenin tek `SynchronizationContext` bir mantıksal iş parçacığını zorlamak için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="263b9-231">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/lifecycle) ve tarafından Blazor oluşturulan tüm olay geri çağırmaları bunun `SynchronizationContext`üzerinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="263b9-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="263b9-232">Bir bileşenin bir zamanlayıcı veya diğer bildirimler gibi dış bir olaya göre güncellenmesi gerekir, ' a gönderilen `InvokeAsync` Blazoryöntemini kullanın. `SynchronizationContext`</span><span class="sxs-lookup"><span data-stu-id="263b9-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="263b9-233">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="263b9-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="263b9-234">Bir tekın `NotifierService` olarak kaydolun:</span><span class="sxs-lookup"><span data-stu-id="263b9-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="263b9-235">Webassembly ' de Blazor hizmeti hizmetine kaydedin `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="263b9-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="263b9-236">Blazor Sunucusunda, hizmetini hizmetine kaydedin `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="263b9-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="263b9-237">Bir bileşeni `NotifierService` güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="263b9-237">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="263b9-238">`NotifierService` Yukarıdaki örnekte, `OnNotify` bileşen yöntemini Blazoröğesinin `SynchronizationContext`dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="263b9-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="263b9-239">`InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="263b9-240">Öğe \@ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="263b9-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="263b9-241">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazorbu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="263b9-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="263b9-242">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="263b9-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="263b9-243">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="263b9-243">Consider the following example:</span></span>

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

<span data-ttu-id="263b9-244">`People` Koleksiyonun içeriği, ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="263b9-245">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı `Details` parametre değerleri almak için değişebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="263b9-246">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="263b9-247">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="263b9-248">Eşleme işlemi, [`@key`](xref:mvc/views/razor#key) Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="263b9-249">`@key`, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="263b9-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="263b9-250">`People` Koleksiyon değiştiğinde, yayılma algoritması örnekler ve `<DetailsEditor>` `person` örnekler arasındaki ilişkilendirmeyi korur:</span><span class="sxs-lookup"><span data-stu-id="263b9-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="263b9-251">Bir `Person` , `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="263b9-252">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="263b9-253">Listedeki bir `Person` konuma eklenirse, ilgili konuma bir yeni `<DetailsEditor>` örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="263b9-254">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="263b9-255">Girişler `Person` yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="263b9-256">Bazı senaryolarda, kullanımı rerendering karmaşıklığını `@key` en aza indirir ve odak konumu gibi Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="263b9-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="263b9-257">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="263b9-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="263b9-258">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="263b9-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="263b9-259">Anahtar ne zaman \@kullanılır?</span><span class="sxs-lookup"><span data-stu-id="263b9-259">When to use \@key</span></span>

<span data-ttu-id="263b9-260">Genellikle, bir liste işlendiğinde (örneğin `@key` , bir `@foreach` blokta) ve tanımlamak için uygun bir değer varsa, `@key`bu işlem kullanım açısından mantıklı olur.</span><span class="sxs-lookup"><span data-stu-id="263b9-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="263b9-261">Bir nesne değiştiğinde bir `@key` öğeyi veya Blazor bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="263b9-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="263b9-262">Değişiklik `@currentPerson` olursa, `@key` öznitelik yönergesi tüm Blazor `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="263b9-263">Değişiklik sırasında `@currentPerson` hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="263b9-264">\@Anahtar ne zaman kullanılmaz?</span><span class="sxs-lookup"><span data-stu-id="263b9-264">When not to use \@key</span></span>

<span data-ttu-id="263b9-265">İle `@key`yayılma yaparken bir performans maliyeti vardır.</span><span class="sxs-lookup"><span data-stu-id="263b9-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="263b9-266">Performans maliyeti büyük değildir, ancak yalnızca öğenin veya `@key` bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="263b9-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="263b9-267">`@key` Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="263b9-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="263b9-268">Kullanmanın `@key` tek avantajı model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="263b9-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="263b9-269">Anahtar için \@kullanılacak değerler</span><span class="sxs-lookup"><span data-stu-id="263b9-269">What values to use for \@key</span></span>

<span data-ttu-id="263b9-270">Genellikle, için `@key`aşağıdaki değer türlerinden birini sağlamak mantıklı olur:</span><span class="sxs-lookup"><span data-stu-id="263b9-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="263b9-271">Model nesne örnekleri (örneğin, `Person` önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="263b9-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="263b9-272">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="263b9-273">Benzersiz tanımlayıcılar (örneğin, veya `int` `string` `Guid`türündeki birincil anahtar değerleri).</span><span class="sxs-lookup"><span data-stu-id="263b9-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="263b9-274">Bu değerlerin çakışmayın için `@key` kullanıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="263b9-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="263b9-275">Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="263b9-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="263b9-276">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="263b9-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="263b9-277">Kendi parametre özelliklerine yazan bileşenler oluşturmayın</span><span class="sxs-lookup"><span data-stu-id="263b9-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="263b9-278">Parametreleri aşağıdaki koşullarda üzerine yazılır:</span><span class="sxs-lookup"><span data-stu-id="263b9-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="263b9-279">Bir `RenderFragment`alt bileşenin içeriği ile işlenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="263b9-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="263b9-281">Üst bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> çağrıldığında ve alt bileşene yeni parametre değerleri sağlandığında parametreler sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="263b9-282">Aşağıdaki `Expander` bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="263b9-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="263b9-283">Alt içeriği işler.</span><span class="sxs-lookup"><span data-stu-id="263b9-283">Renders child content.</span></span>
* <span data-ttu-id="263b9-284">Bileşen parametresiyle alt içeriğin gösterilmesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="263b9-284">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="263b9-285">Bileşen `Expander` , çağıraetkileyebilecek `StateHasChanged`bir üst bileşene eklenir:</span><span class="sxs-lookup"><span data-stu-id="263b9-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="263b9-286">Başlangıçta, `Expander` bileşenleri `Expanded` özellikleri bir kez değiştiğinde bağımsız olarak davranır.</span><span class="sxs-lookup"><span data-stu-id="263b9-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="263b9-287">Alt bileşenler, durumlarını beklendiği gibi korur.</span><span class="sxs-lookup"><span data-stu-id="263b9-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="263b9-288">`StateHasChanged` Üst öğede çağrıldığında, ilk alt bileşenin `Expanded` parametresi ilk değeri (`true`) olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="263b9-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="263b9-289">İkinci bileşende `Expander` hiçbir alt `Expanded` içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.</span><span class="sxs-lookup"><span data-stu-id="263b9-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="263b9-290">Önceki senaryodaki durumu korumak için `Expander` bileşen içindeki özel bir *alanı* kullanarak, onun geçiş durumunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="263b9-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="263b9-291">Aşağıdaki `Expander` bileşen:</span><span class="sxs-lookup"><span data-stu-id="263b9-291">The following `Expander` component:</span></span>

* <span data-ttu-id="263b9-292">Üst öğeden `Expanded` bileşen parametre değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="263b9-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="263b9-293">[OnInitialized olaydaki](xref:blazor/lifecycle#component-initialization-methods)bir *özel alana* (`expanded`) bileşen parametre değerini atar.</span><span class="sxs-lookup"><span data-stu-id="263b9-293">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="263b9-294">İç geçiş durumunu korumak için özel alanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="263b9-295">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="263b9-295">Partial class support</span></span>

Razor<span data-ttu-id="263b9-296">bileşenler kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="263b9-296"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="263b9-297">bileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="263b9-297"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="263b9-298">C# kodu, tek bir dosyada [`@code`](xref:mvc/views/razor#code) HTML işaretlemesi ve Razor kodu olan bir blokta tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="263b9-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="263b9-299">Şablonlar, Razor bu yaklaşımı kullanarak bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="263b9-300">C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="263b9-301">Aşağıdaki örnek, bir `Counter` `@code` Blazor şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir.</span><span class="sxs-lookup"><span data-stu-id="263b9-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="263b9-302">HTML Markup, Razor Code ve C# kodu aynı dosyada:</span><span class="sxs-lookup"><span data-stu-id="263b9-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="263b9-303">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="263b9-304">`Counter` Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="263b9-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="263b9-305">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="263b9-306">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="263b9-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="263b9-307">Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin.</span><span class="sxs-lookup"><span data-stu-id="263b9-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="263b9-308">Bileşenler tarafından Razor kullanılan tipik ad alanları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="263b9-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="263b9-309">Temel sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="263b9-309">Specify a base class</span></span>

<span data-ttu-id="263b9-310">[`@inherits`](xref:mvc/views/razor#inherits) Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="263b9-311">Aşağıdaki örnek, bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel `BlazorRocksBase`sınıfı nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="263b9-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="263b9-312">Taban sınıfın türevi olması gerekir `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="263b9-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="263b9-313">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="263b9-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="263b9-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="263b9-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="263b9-315">Bir öznitelik belirtin</span><span class="sxs-lookup"><span data-stu-id="263b9-315">Specify an attribute</span></span>

<span data-ttu-id="263b9-316">Öznitelikler, [`@attribute`](xref:mvc/views/razor#attribute) yönergeyle birlikte Razor bileşenlerde belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="263b9-317">Aşağıdaki örnek, bileşen sınıfına `[Authorize]` özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="263b9-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="263b9-318">Bileşenleri içeri aktar</span><span class="sxs-lookup"><span data-stu-id="263b9-318">Import components</span></span>

<span data-ttu-id="263b9-319">İle Razor yazılmış bir bileşenin ad alanı, tabanlıdır (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="263b9-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="263b9-320">[`@namespace`](xref:mvc/views/razor#namespace)dosya (*. Razor*) biçimlendirmesinde atama (`@namespace BlazorSample.MyNamespace`). Razor</span><span class="sxs-lookup"><span data-stu-id="263b9-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="263b9-321">`RootNamespace` Proje, proje dosyasında (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="263b9-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="263b9-322">Proje dosyasının dosya adından (*. csproj*) ve proje kökünden bileşen yolundan alınan proje adı.</span><span class="sxs-lookup"><span data-stu-id="263b9-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="263b9-323">Örneğin, çerçeve ad alanına `BlazorSample.Pages` *{Project root}/Pages/Index.Razor* (*BlazorSample. csproj*) çözümleniyor.</span><span class="sxs-lookup"><span data-stu-id="263b9-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="263b9-324">Bileşenler C# ad bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="263b9-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="263b9-325">Bu örnekteki `Index` bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:</span><span class="sxs-lookup"><span data-stu-id="263b9-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="263b9-326">Aynı klasörde, *sayfalarda*.</span><span class="sxs-lookup"><span data-stu-id="263b9-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="263b9-327">Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.</span><span class="sxs-lookup"><span data-stu-id="263b9-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="263b9-328">Farklı bir ad alanında tanımlanan bileşenler, ' ın Razor [`@using`](xref:mvc/views/razor#using) yönergesi kullanılarak kapsama alınır.</span><span class="sxs-lookup"><span data-stu-id="263b9-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="263b9-329">*BlazorSample/Shared/* klasöründe başka bir bileşen `Index.razor` `@using` `NavMenu.razor`varsa, bileşeni aşağıdaki ifadesiyle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="263b9-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="263b9-330">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`](xref:mvc/views/razor#using) yönergeyi gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="263b9-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="263b9-331">`global::` Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="263b9-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="263b9-332">Diğer ad `using` deyimleri (örneğin, `@using Foo = Bar`) ile bileşenleri içeri aktarma desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="263b9-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="263b9-333">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="263b9-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="263b9-334">Örneğin, ile `@using BlazorSample` `<Shared.NavMenu></Shared.NavMenu>` ekleme ve başvuru `NavMenu.razor` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="263b9-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="263b9-335">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="263b9-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="263b9-336">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="263b9-337">Değer veya `false` `null`ise, öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="263b9-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="263b9-338">Değer ise `true`, öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="263b9-339">Aşağıdaki örnekte, `IsCompleted` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini `checked` belirler:</span><span class="sxs-lookup"><span data-stu-id="263b9-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="263b9-340">`IsCompleted` İse `true`, onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="263b9-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="263b9-341">`IsCompleted` İse `false`, onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="263b9-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="263b9-342">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="263b9-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="263b9-343">.NET türü bir `bool`olduğunda, [Aria-BASıLAN](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)gibi bazı HTML öznitelikleri düzgün şekilde çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="263b9-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="263b9-344">Bu durumlarda, yerine bir `string` tür kullanın. `bool`</span><span class="sxs-lookup"><span data-stu-id="263b9-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="263b9-345">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="263b9-345">Raw HTML</span></span>

<span data-ttu-id="263b9-346">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="263b9-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="263b9-347">Ham HTML işlemek için, HTML içeriğini bir `MarkupString` değerde sarın.</span><span class="sxs-lookup"><span data-stu-id="263b9-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="263b9-348">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="263b9-349">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="263b9-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="263b9-350">Aşağıdaki örnek, bir bileşenin işlenmiş `MarkupString` ÇıKTıSıNA statik HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="263b9-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="263b9-351">Değerleri ve parametreleri basamaklama</span><span class="sxs-lookup"><span data-stu-id="263b9-351">Cascading values and parameters</span></span>

<span data-ttu-id="263b9-352">Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="263b9-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="263b9-353">Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="263b9-354">Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="263b9-355">Tema örneği</span><span class="sxs-lookup"><span data-stu-id="263b9-355">Theme example</span></span>

<span data-ttu-id="263b9-356">Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="263b9-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="263b9-357">*Uıthemeclasses/Themeınfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="263b9-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="263b9-358">Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="263b9-359">`CascadingValue` Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="263b9-360">Örneğin, örnek uygulama, uygulamanın düzenleriyle,`ThemeInfo` `@Body` özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir.</span><span class="sxs-lookup"><span data-stu-id="263b9-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="263b9-361">`ButtonClass`öğesinin `btn-success` bir değeri, düzen bileşeninde atanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="263b9-362">Tüm alt bileşenler bu özelliği `ThemeInfo` basamaklı nesne aracılığıyla kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="263b9-363">`CascadingValuesParametersLayout`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="263b9-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="263b9-364">Basamaklı değerlerin kullanılması için, bileşenler `[CascadingParameter]` özniteliği kullanarak Geçişli Parametreler bildirir.</span><span class="sxs-lookup"><span data-stu-id="263b9-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="263b9-365">Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="263b9-366">Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="263b9-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="263b9-367">Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="263b9-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="263b9-368">`CascadingValuesParametersTheme`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="263b9-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="263b9-369">Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, her `Name` `CascadingValue` bileşene ve karşılık gelen `CascadingParameter`öğesine benzersiz bir dize sağlayın.</span><span class="sxs-lookup"><span data-stu-id="263b9-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="263b9-370">Aşağıdaki örnekte, iki `CascadingValue` bileşen adına `MyCascadingType` göre farklı örneklerini basamakla:</span><span class="sxs-lookup"><span data-stu-id="263b9-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="263b9-371">Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:</span><span class="sxs-lookup"><span data-stu-id="263b9-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="263b9-372">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="263b9-372">TabSet example</span></span>

<span data-ttu-id="263b9-373">Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="263b9-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="263b9-374">Örneğin, örnek uygulamada aşağıdaki *Tabset* örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="263b9-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="263b9-375">Örnek uygulamada, sekmelerin uygulandığı `ITab` bir arabirim vardır:</span><span class="sxs-lookup"><span data-stu-id="263b9-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="263b9-376">Bileşen, çeşitli `Tab` bileşenleri `TabSet` içeren bileşenini kullanır: `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="263b9-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="263b9-377">Alt `Tab` bileşenler, `TabSet`öğesine açıkça parametre olarak aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="263b9-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="263b9-378">Bunun yerine, alt `Tab` bileşenleri öğesinin alt içeriğinin bir parçasıdır `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="263b9-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="263b9-379">Bununla birlikte, `TabSet` üst bilgileri ve etkin sekmeyi işleyebilmesi için her `Tab` bileşen hakkında hala bilmeniz gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için `TabSet` bileşen, kendisini alt `Tab` bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* .</span><span class="sxs-lookup"><span data-stu-id="263b9-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="263b9-380">`TabSet`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="263b9-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="263b9-381">Alt `Tab` bileşenler, kapsayan `TabSet` ' i basamaklı bir parametre olarak yakalar, bu `Tab` nedenle bileşenler, bu sekmenin `TabSet` etkin olduğu ve koordinasyonu üzerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="263b9-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="263b9-382">`Tab`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="263b9-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="263b9-383">şablondan</span><span class="sxs-lookup"><span data-stu-id="263b9-383"> templates</span></span>

<span data-ttu-id="263b9-384">İşleme parçaları, şablon sözdizimi kullanılarak Razor tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-384">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="263b9-385">Şablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="263b9-385"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="263b9-386">Aşağıdaki örnek, bir bileşeni doğrudan bir `RenderFragment` bileşende `RenderFragment<T>` nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="263b9-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="263b9-387">Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="263b9-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="263b9-388">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="263b9-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="263b9-389">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="263b9-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="263b9-390">Bu Blazor yana, ölçeklenebilir vektör GRAFIKLERI (SVG) görüntüleri (*. SVG*) dahil olmak üzere HTML, tarayıcı tarafından desteklenen görüntüler `<img>` etiketi aracılığıyla desteklenir:</span><span class="sxs-lookup"><span data-stu-id="263b9-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="263b9-391">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının (*. css*) CSS kurallarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="263b9-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="263b9-392">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="263b9-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="263b9-393">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına (*. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="263b9-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="263b9-394">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve `@bind` bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="263b9-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="263b9-395">Gelecekteki bir sürümde bu sınırlamaları ele almayı bekliyoruz.</span><span class="sxs-lookup"><span data-stu-id="263b9-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="263b9-396">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="263b9-396">Additional resources</span></span>

* <span data-ttu-id="263b9-397"><xref:security/blazor/server/threat-mitigation>&ndash; Kaynak tükenmesi ile Çekişmek zorunda olması gereken sunucu uygulamaları oluşturmaya Blazor yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="263b9-397"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
