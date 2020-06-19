---
title: ASP.NET Core bileşenleri oluşturma ve kullanma Razor
author: guardrex
description: RazorVerileri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: a97fbe07251a61b30985695e3d207f0e3c3a777b
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103894"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="4532e-103">ASP.NET Core bileşenleri oluşturma ve kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="4532e-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="4532e-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="4532e-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="4532e-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4532e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="4532e-106">uygulamalar, *Bileşenler*kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="4532e-106"> apps are built using *components*.</span></span> <span data-ttu-id="4532e-107">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="4532e-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="4532e-108">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="4532e-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="4532e-109">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="4532e-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="4532e-110">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="4532e-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="4532e-111">Component classes</span></span>

<span data-ttu-id="4532e-112">Bileşenler, [Razor](xref:mvc/views/razor) C# ve HTML biçimlendirme birleşimi kullanılarak bileşen dosyalarında (*. Razor*) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="4532e-113">İçindeki bir bileşen Blazor bir \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a>Razor<span data-ttu-id="4532e-114">sözdizimi</span><span class="sxs-lookup"><span data-stu-id="4532e-114"> syntax</span></span>

Razor<span data-ttu-id="4532e-115">uygulamalardaki bileşenler Blazor yaygın olarak Razor söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-115"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="4532e-116">RazorBiçimlendirme diline alışkın değilseniz, devam etmeden önce okumanız önerilir <xref:mvc/views/razor> .</span><span class="sxs-lookup"><span data-stu-id="4532e-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="4532e-117">Söz dizimi üzerindeki içeriğe erişirken Razor , aşağıdaki bölümlere özel bir dikkat ödeyin:</span><span class="sxs-lookup"><span data-stu-id="4532e-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="4532e-118">[Yönergeler](xref:mvc/views/razor#directives): `@` -genellikle bileşen biçimlendirmesinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="4532e-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="4532e-119">[Yönerge öznitelikleri](xref:mvc/views/razor#directive-attributes): `@` -önek olarak bileşen öğelerinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="4532e-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="4532e-120">Adlar</span><span class="sxs-lookup"><span data-stu-id="4532e-120">Names</span></span>

<span data-ttu-id="4532e-121">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="4532e-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="4532e-122">Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="4532e-122">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="4532e-123">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="4532e-123">Routing</span></span>

<span data-ttu-id="4532e-124">Uygulamasında yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="4532e-125">Razor [`@page`][9] Yönergeyle bir dosya derlendiğinde, oluşturulan sınıfa <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> yol şablonunu belirten bir değer verilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="4532e-126">Çalışma zamanında, yönlendirici bileşen sınıflarını bir ile arar <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ve hangi bileşenin Istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="4532e-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="4532e-127">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="4532e-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="4532e-128">İşaretleme</span><span class="sxs-lookup"><span data-stu-id="4532e-128">Markup</span></span>

<span data-ttu-id="4532e-129">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="4532e-130">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir *Razor* .</span><span class="sxs-lookup"><span data-stu-id="4532e-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="4532e-131">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4532e-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="4532e-132">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="4532e-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="4532e-133">Bileşen sınıfının üyeleri bir [`@code`][1] blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="4532e-134">[`@code`][1]Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="4532e-135">Birden çok [`@code`][1] blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="4532e-136">Bileşen üyeleri, ile başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir `@` .</span><span class="sxs-lookup"><span data-stu-id="4532e-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="4532e-137">Örneğin, bir C# alanı alan adının önüne eklenerek işlenir `@` .</span><span class="sxs-lookup"><span data-stu-id="4532e-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="4532e-138">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="4532e-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="4532e-139">`headingFontStyle`için CSS özellik değerine `font-style` .</span><span class="sxs-lookup"><span data-stu-id="4532e-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="4532e-140">`headingText``<h1>`öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="4532e-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="4532e-141">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4532e-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="4532e-142">ardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.</span><span class="sxs-lookup"><span data-stu-id="4532e-142"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="4532e-143">Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="4532e-144">Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="4532e-144">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="4532e-145">Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-145">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="4532e-146">Ad alanları</span><span class="sxs-lookup"><span data-stu-id="4532e-146">Namespaces</span></span>

<span data-ttu-id="4532e-147">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="4532e-148">Uygulamanın kök ad alanı ise `BlazorApp` ve `Counter` bileşen *Sayfalar* klasöründe bulunuyorsa:</span><span class="sxs-lookup"><span data-stu-id="4532e-148">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="4532e-149">`Counter`Bileşenin ad alanı `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="4532e-149">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="4532e-150">Bileşenin tam nitelikli tür adı `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="4532e-150">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="4532e-151">Bileşenleri tutan özel klasörler için, [`@using`][2] üst bileşene veya uygulamanın *_Imports. Razor* dosyasına bir yönerge ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4532e-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="4532e-152">Aşağıdaki örnek *Bileşenler* klasöründeki bileşenleri kullanılabilir hale getirir:</span><span class="sxs-lookup"><span data-stu-id="4532e-152">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="4532e-153">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`][2] yönergeyi gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="4532e-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="4532e-154">İle yazılmış bir bileşenin ad alanı, Razor tabanlıdır (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="4532e-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="4532e-155">[`@namespace`][8]Razordosya (*. Razor*) biçimlendirmesinde atama ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="4532e-155">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="4532e-156">Proje, `RootNamespace` Proje dosyasında ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="4532e-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="4532e-157">Proje dosyasının dosya adından (*. csproj*) ve proje kökünden bileşen yolundan alınan proje adı.</span><span class="sxs-lookup"><span data-stu-id="4532e-157">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="4532e-158">Örneğin, çerçeve ad alanına *{Project root}/Pages/Index.Razor* (*BlazorSample. csproj*) çözümleniyor `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="4532e-158">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="4532e-159">Bileşenler C# ad bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="4532e-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="4532e-160">`Index`Bu örnekteki bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:</span><span class="sxs-lookup"><span data-stu-id="4532e-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="4532e-161">Aynı klasörde, *sayfalarda*.</span><span class="sxs-lookup"><span data-stu-id="4532e-161">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="4532e-162">Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.</span><span class="sxs-lookup"><span data-stu-id="4532e-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="4532e-163">`global::`Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="4532e-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="4532e-164">Deyimleri [kullanan](/dotnet/csharp/language-reference/keywords/using-statement) (örneğin,) bileşenleri içeri aktarma `@using Foo = Bar` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="4532e-164">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="4532e-165">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="4532e-166">Örneğin, `@using BlazorSample` `NavMenu` ile bileşeni () ekleme ve başvuru `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="4532e-167">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="4532e-167">Partial class support</span></span>

Razor<span data-ttu-id="4532e-168">bileşenler kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4532e-168"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="4532e-169">bileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="4532e-169"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="4532e-170">C# kodu, [`@code`][1] tek bir dosyada HTML işaretlemesi ve kodu olan bir blokta tanımlanmıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="4532e-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="4532e-171">Şablonlar, Razor Bu yaklaşımı kullanarak bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="4532e-171"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="4532e-172">C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="4532e-173">Aşağıdaki örnek, `Counter` [`@code`][1] bir şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir Blazor .</span><span class="sxs-lookup"><span data-stu-id="4532e-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="4532e-174">HTML Markup, Razor Code ve C# kodu aynı dosyada:</span><span class="sxs-lookup"><span data-stu-id="4532e-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="4532e-175">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-175">*Counter.razor*:</span></span>

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

<span data-ttu-id="4532e-176">`Counter`Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="4532e-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="4532e-177">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-177">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="4532e-178">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="4532e-178">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="4532e-179">Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4532e-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="4532e-180">Bileşenler tarafından kullanılan tipik ad alanları Razor şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4532e-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

### <a name="specify-a-base-class"></a><span data-ttu-id="4532e-181">Temel sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="4532e-181">Specify a base class</span></span>

<span data-ttu-id="4532e-182">[`@inherits`][6]Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-182">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="4532e-183">Aşağıdaki örnek, bileşenin `BlazorRocksBase` özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel sınıfı nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="4532e-183">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="4532e-184">Taban sınıfın türevi olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="4532e-184">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="4532e-185">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-185">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="4532e-186">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="4532e-186">*BlazorRocksBase.cs*:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="4532e-187">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="4532e-187">Use components</span></span>

<span data-ttu-id="4532e-188">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-188">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="4532e-189">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="4532e-189">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="4532e-190">*Index. Razor* dosyasında aşağıdaki biçimlendirme bir örneği işler `HeadingComponent` :</span><span class="sxs-lookup"><span data-stu-id="4532e-190">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="4532e-191">*Bileşenler/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-191">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="4532e-192">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-192">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="4532e-193">[`@using`][2]Bileşenin ad alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="4532e-193">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="4532e-194">Parametreler</span><span class="sxs-lookup"><span data-stu-id="4532e-194">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="4532e-195">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="4532e-195">Route parameters</span></span>

<span data-ttu-id="4532e-196">Bileşenler, yönergede belirtilen yol şablonundan rota parametreleri alabilir [`@page`][9] .</span><span class="sxs-lookup"><span data-stu-id="4532e-196">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="4532e-197">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-197">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="4532e-198">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-198">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="4532e-199">İsteğe bağlı parametreler desteklenmez, bu nedenle [`@page`][9] Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-199">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="4532e-200">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="4532e-200">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="4532e-201">İkinci [`@page`][9] yönerge, `{text}` route parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="4532e-201">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="4532e-202">*Catch-all* `*` / `**` Birden çok klasör sınırlarındaki yolu yakalayan catch-all parametresi sözdizimi () **,** Razor bileşenlerde (*. Razor*) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-202">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="4532e-203">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="4532e-203">Component parameters</span></span>

<span data-ttu-id="4532e-204">Bileşenler, bileşen sınıfında özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="4532e-204">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="4532e-205">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-205">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="4532e-206">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-206">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="4532e-207">Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` öğesinin özelliğinin değerini ayarlar `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="4532e-207">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="4532e-208">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-208">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="4532e-209">Kendi *bileşen parametrelerine*yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-209">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="4532e-210">Daha fazla bilgi için [kendi parametre özelliklerine yazan bileşenleri oluşturma](#dont-create-components-that-write-to-their-own-parameter-properties) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4532e-210">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="4532e-211">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="4532e-211">Child content</span></span>

<span data-ttu-id="4532e-212">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-212">Components can set the content of another component.</span></span> <span data-ttu-id="4532e-213">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="4532e-213">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="4532e-214">Aşağıdaki örnekte, öğesinin `ChildComponent` `ChildContent` <xref:Microsoft.AspNetCore.Components.RenderFragment> işlemek için bir kullanıcı arabirimi segmentini temsil eden öğesini temsil eden bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="4532e-214">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="4532e-215">Değeri, `ChildContent` bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-215">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="4532e-216">Değeri, `ChildContent` ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="4532e-216">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="4532e-217">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-217">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="4532e-218">İçeriği alan özelliğin <xref:Microsoft.AspNetCore.Components.RenderFragment> `ChildContent` kural tarafından adlandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4532e-218">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="4532e-219">`ParentComponent`Örnek uygulamadaki ' de `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz `<ChildComponent>` .</span><span class="sxs-lookup"><span data-stu-id="4532e-219">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="4532e-220">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-220">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="4532e-221">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="4532e-221">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="4532e-222">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-222">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="4532e-223">Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* [`@attributes`][3] Razor .</span><span class="sxs-lookup"><span data-stu-id="4532e-223">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="4532e-224">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="4532e-224">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="4532e-225">Örneğin, `<input>` çok sayıda parametreyi destekleyen bir için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-225">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="4532e-226">Aşağıdaki örnekte, ilk `<input>` öğesi ( `id="useIndividualParams"` ) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe ( `id="useAttributesDict"` ) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="4532e-226">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="4532e-227">Parametrenin türü `IEnumerable<KeyValuePair<string, object>>` dize anahtarlarıyla gerçekleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="4532e-227">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="4532e-228">`IReadOnlyDictionary<string, object>`Bu senaryoda ayrıca bir seçenek de vardır.</span><span class="sxs-lookup"><span data-stu-id="4532e-228">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="4532e-229">`<input>`Her iki yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="4532e-229">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="4532e-230">Rastgele öznitelikleri kabul etmek için [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) özelliği olarak ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` :</span><span class="sxs-lookup"><span data-stu-id="4532e-230">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="4532e-231"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Üzerindeki özelliği, [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4532e-231">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="4532e-232">Bir bileşen yalnızca ile tek bir parametre tanımlayabilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="4532e-232">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="4532e-233">İle kullanılan özellik türü <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `Dictionary<string, object>` dize anahtarlarıyla atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4532e-233">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="4532e-234">`IEnumerable<KeyValuePair<string, object>>``IReadOnlyDictionary<string, object>`Ayrıca, Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="4532e-234">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="4532e-235">[`@attributes`][3]Öğe özniteliklerinin konumuna göreli konumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="4532e-235">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="4532e-236">Öğe üzerinde ne zaman bırakıldığında [`@attributes`][3] , öznitelikler sağdan sola (son-ilk) işlenir.</span><span class="sxs-lookup"><span data-stu-id="4532e-236">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="4532e-237">Bir bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun `Child` :</span><span class="sxs-lookup"><span data-stu-id="4532e-237">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="4532e-238">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-238">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4532e-239">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-239">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4532e-240">`Child`Bileşenin `extra` özniteliği öğesinin sağına ayarlanır [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="4532e-240">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="4532e-241">`Parent` `<div>` `extra="5"` Öznitelikler sağdan sola (en son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:</span><span class="sxs-lookup"><span data-stu-id="4532e-241">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="4532e-242">Aşağıdaki örnekte, `extra` ve sırası [`@attributes`][3] `Child` bileşen tarafından tersine çevrilir `<div>` :</span><span class="sxs-lookup"><span data-stu-id="4532e-242">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="4532e-243">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-243">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4532e-244">*Childcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4532e-244">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4532e-245">Bileşendeki işlenen `<div>` `Parent` `extra="10"` ek öznitelik ile geçirildiğinde şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="4532e-245">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="4532e-246">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="4532e-246">Capture references to components</span></span>

<span data-ttu-id="4532e-247">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya gibi komutları bu örneğe verebilirsiniz `Show` `Reset` .</span><span class="sxs-lookup"><span data-stu-id="4532e-247">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="4532e-248">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="4532e-248">To capture a component reference:</span></span>

* <span data-ttu-id="4532e-249">[`@ref`][4]Alt bileşene bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4532e-249">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="4532e-250">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="4532e-250">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="4532e-251">Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="4532e-251">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="4532e-252">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4532e-252">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4532e-253">`loginDialog`Değişken yalnızca bileşen işlendikten sonra ve çıktısı öğesi içerdiğinde doldurulur `MyLoginDialog` .</span><span class="sxs-lookup"><span data-stu-id="4532e-253">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="4532e-254">Bu noktaya kadar başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="4532e-254">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="4532e-255">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için [Onafterrenderasync veya OnAfterRender yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-255">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="4532e-256">Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="4532e-256">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="4532e-257">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="4532e-257">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="4532e-258">Bileşen başvuruları JavaScript koduna geçirilmiyor.</span><span class="sxs-lookup"><span data-stu-id="4532e-258">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="4532e-259">Bileşen başvuruları yalnızca .NET kodunda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-259">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="4532e-260">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="4532e-260">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="4532e-261">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-261">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="4532e-262">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="4532e-262">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="4532e-263">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="4532e-263">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="4532e-264"><xref:System.Threading.SynchronizationContext>yürütmenin tek bir mantıksal iş parçacığını zorlamak için bir eşitleme bağlamı () kullanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-264"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="4532e-265">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/components/lifecycle) ve tarafından oluşturulan tüm olay geri çağırmaları Blazor eşitleme bağlamında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="4532e-265">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="4532e-266">Sunucunun eşitleme bağlamı, tek iş parçacıklı bir ortamı öykünmeye çalışır ve bu sayede tek iş parçacıklı olan tarayıcıdaki WebAssembly modeliyle yakından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="4532e-266"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="4532e-267">Belirli bir zamanda, iş, tek bir mantıksal iş parçacığının izlenimi vererek tam olarak bir iş parçacığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-267">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="4532e-268">Aynı anda iki işlem yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-268">No two operations execute concurrently.</span></span>

<span data-ttu-id="4532e-269">Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi bir dış olay temel alınarak güncellenmesi gerekir, bu `InvokeAsync` yöntemi, Blazor eşitleme bağlamına dağımakta olan yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-269">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="4532e-270">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="4532e-270">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="4532e-271">`NotifierService`Bir tekın olarak kaydolun:</span><span class="sxs-lookup"><span data-stu-id="4532e-271">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="4532e-272">BlazorWebassembly ' de hizmeti hizmetine kaydedin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="4532e-272">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="4532e-273">BlazorSunucusunda, hizmetini hizmetine kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4532e-273">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="4532e-274">`NotifierService`Bir bileşeni güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="4532e-274">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="4532e-275">Önceki örnekte, `NotifierService` bileşen `OnNotify` metodunu Blazor eşitleme bağlamı dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="4532e-275">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="4532e-276">`InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-276">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="4532e-277">\@Öğe ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="4532e-277">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="4532e-278">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor Bu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="4532e-278">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="4532e-279">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="4532e-279">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="4532e-280">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4532e-280">Consider the following example:</span></span>

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

<span data-ttu-id="4532e-281">Koleksiyonun içeriği, `People` ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-281">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="4532e-282">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı parametre değerleri almak için değişebilir `Details` .</span><span class="sxs-lookup"><span data-stu-id="4532e-282">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="4532e-283">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-283">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="4532e-284">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-284">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="4532e-285">Eşleme işlemi, [`@key`][5] Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-285">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="4532e-286">[`@key`][5], anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="4532e-286">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="4532e-287">`People`Koleksiyon değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekler ve örnekler arasındaki ilişkilendirmeyi korur `person` :</span><span class="sxs-lookup"><span data-stu-id="4532e-287">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="4532e-288">Bir, `Person` `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-288">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="4532e-289">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-289">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4532e-290">Listedeki bir `Person` konuma eklenirse, `<DetailsEditor>` ilgili konuma bir yeni örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="4532e-290">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="4532e-291">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-291">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4532e-292">`Person`Girişler yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-292">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="4532e-293">Bazı senaryolarda, kullanımı [`@key`][5] rerendering karmaşıklığını en aza indirir ve odak konumu gıbı Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="4532e-293">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4532e-294">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="4532e-294">Keys are local to each container element or component.</span></span> <span data-ttu-id="4532e-295">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="4532e-295">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="4532e-296">Anahtar ne zaman kullanılır? \@</span><span class="sxs-lookup"><span data-stu-id="4532e-296">When to use \@key</span></span>

<span data-ttu-id="4532e-297">Genellikle, [`@key`][5] her bir liste işlendiğinde (örneğin, bir [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) bloğunda) ve tanımlamak için uygun bir değer varsa, bu durum kullanılır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="4532e-297">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="4532e-298">Bir [`@key`][5] Blazor nesne değiştiğinde bir öğeyi veya bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4532e-298">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="4532e-299">`@currentPerson`Değişiklik olursa, [`@key`][5] öznitelik yönergesi Blazor tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="4532e-299">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="4532e-300">Değişiklik sırasında hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="4532e-300">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="4532e-301">Anahtar ne zaman kullanılmaz? \@</span><span class="sxs-lookup"><span data-stu-id="4532e-301">When not to use \@key</span></span>

<span data-ttu-id="4532e-302">İle yayılma yaparken bir performans maliyeti vardır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="4532e-302">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="4532e-303">Performans maliyeti büyük değildir, ancak yalnızca [`@key`][5] öğenin veya bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="4532e-303">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="4532e-304">[`@key`][5]Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="4532e-304">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="4532e-305">Kullanmanın tek avantajı [`@key`][5] model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="4532e-305">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="4532e-306">Anahtar için kullanılacak değerler \@</span><span class="sxs-lookup"><span data-stu-id="4532e-306">What values to use for \@key</span></span>

<span data-ttu-id="4532e-307">Genellikle, için aşağıdaki değer türlerinden birini sağlamak mantıklı olur [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="4532e-307">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="4532e-308">Model nesne örnekleri (örneğin, `Person` Önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="4532e-308">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="4532e-309">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4532e-309">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="4532e-310">Benzersiz tanımlayıcılar (örneğin, veya türündeki birincil anahtar değerleri `int` `string` `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="4532e-310">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="4532e-311">Bu değerlerin çakışmayın için kullanıldığından emin olun [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="4532e-311">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="4532e-312">Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4532e-312">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="4532e-313">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-313">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="4532e-314">Kendi parametre özelliklerine yazan bileşenler oluşturmayın</span><span class="sxs-lookup"><span data-stu-id="4532e-314">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="4532e-315">Parametreleri aşağıdaki koşullarda üzerine yazılır:</span><span class="sxs-lookup"><span data-stu-id="4532e-315">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="4532e-316">Bir alt bileşenin içeriği ile işlenir <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="4532e-316">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="4532e-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4532e-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="4532e-318">Üst bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> çağrıldığında ve alt bileşene yeni parametre değerleri sağlandığında parametreler sıfırlanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-318">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="4532e-319">Aşağıdaki bileşeni göz önünde bulundurun `Expander` :</span><span class="sxs-lookup"><span data-stu-id="4532e-319">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="4532e-320">Alt içeriği işler.</span><span class="sxs-lookup"><span data-stu-id="4532e-320">Renders child content.</span></span>
* <span data-ttu-id="4532e-321">Bileşen parametresiyle alt içeriğin gösterilmesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="4532e-321">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="4532e-322">`Expander`Bileşen, çağıraetkileyebilecek bir üst bileşene eklenir <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="4532e-322">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="4532e-323">Başlangıçta, `Expander` bileşenleri özellikleri bir kez değiştiğinde bağımsız olarak davranır `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="4532e-323">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="4532e-324">Alt bileşenler, durumlarını beklendiği gibi korur.</span><span class="sxs-lookup"><span data-stu-id="4532e-324">The child components maintain their states as expected.</span></span> <span data-ttu-id="4532e-325"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Üst öğede çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değeri () olarak döndürülür `true` .</span><span class="sxs-lookup"><span data-stu-id="4532e-325">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="4532e-326">İkinci `Expander` `Expanded` bileşende hiçbir alt içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.</span><span class="sxs-lookup"><span data-stu-id="4532e-326">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="4532e-327">Önceki senaryodaki durumu korumak için bileşen içindeki *özel bir alanı* kullanarak, onun geçiş `Expander` durumunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="4532e-327">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="4532e-328">Aşağıdaki `Expander` bileşen:</span><span class="sxs-lookup"><span data-stu-id="4532e-328">The following `Expander` component:</span></span>

* <span data-ttu-id="4532e-329">Üst öğeden `Expanded` bileşen parametre değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4532e-329">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="4532e-330">*private field* `expanded` [OnInitialized olaydaki](xref:blazor/components/lifecycle#component-initialization-methods)bir özel alana () bileşen parametre değerini atar.</span><span class="sxs-lookup"><span data-stu-id="4532e-330">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="4532e-331">İç geçiş durumunu korumak için özel alanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="4532e-331">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="apply-an-attribute"></a><span data-ttu-id="4532e-332">Öznitelik uygulama</span><span class="sxs-lookup"><span data-stu-id="4532e-332">Apply an attribute</span></span>

<span data-ttu-id="4532e-333">Öznitelikler Razor , yönergeyle birlikte bileşenlere uygulanabilir [`@attribute`][7] .</span><span class="sxs-lookup"><span data-stu-id="4532e-333">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="4532e-334">Aşağıdaki örnek, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşen sınıfına özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="4532e-334">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="4532e-335">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="4532e-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="4532e-336">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="4532e-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="4532e-337">Değer `false` veya ise `null` , öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="4532e-338">Değer ise `true` , öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="4532e-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="4532e-339">Aşağıdaki örnekte, `IsCompleted` `checked` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini belirler:</span><span class="sxs-lookup"><span data-stu-id="4532e-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="4532e-340">`IsCompleted`İse `true` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="4532e-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="4532e-341">`IsCompleted`İse `false` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="4532e-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="4532e-342">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="4532e-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="4532e-343">.NET türü bir olduğunda, [Aria-basılan](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)gıbı bazı HTML öznitelikleri düzgün şekilde çalışmaz `bool` .</span><span class="sxs-lookup"><span data-stu-id="4532e-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="4532e-344">Bu durumlarda, `string` yerine bir tür kullanın `bool` .</span><span class="sxs-lookup"><span data-stu-id="4532e-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="4532e-345">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="4532e-345">Raw HTML</span></span>

<span data-ttu-id="4532e-346">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="4532e-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="4532e-347">Ham HTML işlemek için, HTML içeriğini bir değerde sarın `MarkupString` .</span><span class="sxs-lookup"><span data-stu-id="4532e-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="4532e-348">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="4532e-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="4532e-349">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="4532e-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="4532e-350">Aşağıdaki örnek, `MarkupString` bir bileşenin işlenmiş çıktısına STATIK HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="4532e-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor<span data-ttu-id="4532e-351">şablondan</span><span class="sxs-lookup"><span data-stu-id="4532e-351"> templates</span></span>

<span data-ttu-id="4532e-352">İşleme parçaları, Razor şablon sözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-352">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="4532e-353">Şablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="4532e-353"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="4532e-354">Aşağıdaki örnek, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> bir bileşeni doğrudan bir bileşende nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="4532e-354">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="4532e-355">Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/components/templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4532e-355">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="4532e-356">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="4532e-356">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="4532e-357">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="4532e-357">Static assets</span></span>

Blazor<span data-ttu-id="4532e-358">Projenin [Web kökü (Wwwroot) klasörü](xref:fundamentals/index#web-root)altında statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.</span><span class="sxs-lookup"><span data-stu-id="4532e-358"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="4532e-359">`/`Statik bir varlık için Web köküne başvurmak üzere temel göreli bir yol () kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-359">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="4532e-360">Aşağıdaki örnekte *logo.png* , fiziksel olarak *{Project root}/Wwwroot/Images* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="4532e-360">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="4532e-361">bileşenler, **not** tilde işareti gösterimini ( `~/` ) desteklemez.</span><span class="sxs-lookup"><span data-stu-id="4532e-361"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="4532e-362">Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="4532e-362">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="4532e-363">Etiket Yardımcıları bileşenlerde desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="4532e-363">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="4532e-364">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , Razor bileşenlerinde (*. Razor* dosyaları) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-364">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="4532e-365">' De etiket Yardımcısı benzeri işlevsellik sağlamak için Blazor , etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="4532e-365">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="4532e-366">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="4532e-366">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="4532e-367">Bu yana Blazor , ölçeklenebilir vektör grafikleri (SVG) görüntüleri (*. SVG*) dahIl olmak üzere HTML, tarayıcı tarafından desteklenen görüntüler etiketi aracılığıyla desteklenir `<img>` :</span><span class="sxs-lookup"><span data-stu-id="4532e-367">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="4532e-368">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının (*. css*) CSS kurallarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="4532e-368">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="4532e-369">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4532e-369">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="4532e-370">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına (*. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="4532e-370">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="4532e-371">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve [`@bind`][10] bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="4532e-371">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="4532e-372">Daha fazla bilgi için bkz. [Içindeki SVG desteği Blazor (DotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="4532e-372">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4532e-373">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4532e-373">Additional resources</span></span>

* <span data-ttu-id="4532e-374"><xref:blazor/security/server/threat-mitigation>: Blazor Kaynak tükenmesi ile Çekişmek zorunda olması gereken sunucu uygulamaları oluşturmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="4532e-374"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
