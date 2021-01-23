---
title: ASP.NET Core bileşenleri oluşturma ve kullanma Razor
author: guardrex
description: RazorVerileri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
no-loc:
- appsettings.json
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
uid: blazor/components/index
ms.openlocfilehash: fe30c3b3be92c30dea6d0cb97e642eec1da6328b
ms.sourcegitcommit: 72c8ada9b9644ee4751ed6869e892a8558dd8e66
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723518"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="73ffc-103">ASP.NET Core bileşenleri oluşturma ve kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="73ffc-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="73ffc-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Ade](https://github.com/scottaddie)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="73ffc-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="73ffc-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73ffc-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="73ffc-106">Blazor uygulamalar, *Bileşenler* kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="73ffc-107">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="73ffc-108">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="73ffc-109">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="73ffc-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="73ffc-110">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="73ffc-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="73ffc-111">Component classes</span></span>

<span data-ttu-id="73ffc-112">Bileşenler, [Razor](xref:mvc/views/razor) `.razor` C# ve HTML biçimlendirmesinin bir birleşimi kullanılarak bileşen dosyalarında () uygulanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="73ffc-113">İçindeki bir bileşen Blazor bir *Razor bileşen* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="73ffc-114">Razor sözdizimi</span><span class="sxs-lookup"><span data-stu-id="73ffc-114">Razor syntax</span></span>

<span data-ttu-id="73ffc-115">Razor uygulamalardaki bileşenler Blazor yaygın olarak Razor söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="73ffc-116">RazorBiçimlendirme diline alışkın değilseniz, devam etmeden önce [ Razor ASP.NET Core için sözdizimi başvurusunu](xref:mvc/views/razor) okumanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-116">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="73ffc-117">Söz dizimi üzerindeki içeriğe erişirken Razor , aşağıdaki bölümlere özel bir dikkat ödeyin:</span><span class="sxs-lookup"><span data-stu-id="73ffc-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="73ffc-118">[Yönergeler](xref:mvc/views/razor#directives): `@` -genellikle bileşen biçimlendirmesinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="73ffc-119">[Yönerge öznitelikleri](xref:mvc/views/razor#directive-attributes): `@` -önek olarak bileşen öğelerinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="73ffc-120">Adlar</span><span class="sxs-lookup"><span data-stu-id="73ffc-120">Names</span></span>

<span data-ttu-id="73ffc-121">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="73ffc-122">Örneğin, `MyCoolComponent.razor` geçerlidir ve `myCoolComponent.razor` geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="73ffc-123">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="73ffc-123">Routing</span></span>

<span data-ttu-id="73ffc-124">Uygulamasında yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="73ffc-125">Razor [`@page`][9] Yönergeyle bir dosya derlendiğinde, oluşturulan sınıfa <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> yol şablonunu belirten bir değer verilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="73ffc-126">Çalışma zamanında, yönlendirici bileşen sınıflarını bir ile arar <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ve hangi bileşenin Istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="73ffc-127">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="73ffc-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="73ffc-128">İşaretleme</span><span class="sxs-lookup"><span data-stu-id="73ffc-128">Markup</span></span>

<span data-ttu-id="73ffc-129">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="73ffc-130">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir *Razor* .</span><span class="sxs-lookup"><span data-stu-id="73ffc-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="73ffc-131">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="73ffc-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="73ffc-132">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="73ffc-133">Bileşen sınıfının üyeleri bir [`@code`][1] blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="73ffc-134">[`@code`][1]Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="73ffc-135">Birden çok [`@code`][1] blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="73ffc-136">Bileşen üyeleri, ile başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir `@` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="73ffc-137">Örneğin, bir C# alanı alan adının önüne eklenerek işlenir `@` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="73ffc-138">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="73ffc-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="73ffc-139">`headingFontStyle` için CSS özellik değerine `font-style` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="73ffc-140">`headingText``<h1>`öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="73ffc-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="73ffc-141">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="73ffc-142">Blazor ardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.</span><span class="sxs-lookup"><span data-stu-id="73ffc-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="73ffc-143">Ek ayrıntı içinde sunulmaktadır <xref:blazor/components/rendering> .</span><span class="sxs-lookup"><span data-stu-id="73ffc-143">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="73ffc-144">Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-144">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="73ffc-145">Web sayfalarını üreten bileşenler genellikle `Pages` klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-145">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="73ffc-146">Sayfa olmayan bileşenler sıklıkla `Shared` klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-146">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="73ffc-147">Ad alanları</span><span class="sxs-lookup"><span data-stu-id="73ffc-147">Namespaces</span></span>

<span data-ttu-id="73ffc-148">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-148">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="73ffc-149">Uygulamanın kök ad alanı ise `BlazorSample` ve `Counter` bileşen `Pages` klasöründe bulunuyorsa:</span><span class="sxs-lookup"><span data-stu-id="73ffc-149">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="73ffc-150">`Counter`Bileşenin ad alanı `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-150">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="73ffc-151">Bileşenin tam nitelikli tür adı `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-151">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="73ffc-152">Bileşenleri tutan özel klasörler için, [`@using`][2] üst bileşene veya uygulamanın dosyasına bir yönerge ekleyin `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-152">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="73ffc-153">Aşağıdaki örnek, klasördeki bileşenleri kullanılabilir hale getirir `Components` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-153">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="73ffc-154">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`][2] yönergeyi gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="73ffc-154">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="73ffc-155">İle yazılmış bir bileşenin ad alanı, Razor tabanlıdır (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="73ffc-155">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="73ffc-156">[`@namespace`][8]Razordosya ( `.razor` ) biçimlendirmesinde atama () `@namespace BlazorSample.MyNamespace` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-156">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="73ffc-157">Proje, `RootNamespace` Proje dosyasında ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="73ffc-157">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="73ffc-158">Proje dosyasının dosya adından () alınan proje adı `.csproj` ve proje kökünden bileşen yolu.</span><span class="sxs-lookup"><span data-stu-id="73ffc-158">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="73ffc-159">Örneğin, çerçeve `{PROJECT ROOT}/Pages/Index.razor` `BlazorSample.csproj` ad alanına () çözümler `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-159">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="73ffc-160">Bileşenler C# ad bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-160">Components follow C# name binding rules.</span></span> <span data-ttu-id="73ffc-161">`Index`Bu örnekteki bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-161">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="73ffc-162">Aynı klasörde, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-162">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="73ffc-163">Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-163">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="73ffc-164">`global::`Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="73ffc-164">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="73ffc-165">Diğer ad [`using`](/dotnet/csharp/language-reference/keywords/using-statement) deyimleri (örneğin,) ile bileşenleri içeri aktarma `@using Foo = Bar` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="73ffc-165">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="73ffc-166">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-166">Partially qualified names aren't supported.</span></span> <span data-ttu-id="73ffc-167">Örneğin, `@using BlazorSample` `NavMenu` ile bileşeni () ekleme ve başvuru `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-167">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="73ffc-168">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="73ffc-168">Partial class support</span></span>

<span data-ttu-id="73ffc-169">Razor bileşenler kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-169">Razor components are generated as partial classes.</span></span> <span data-ttu-id="73ffc-170">Razor bileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-170">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="73ffc-171">C# kodu, [`@code`][1] tek bir dosyada HTML işaretlemesi ve kodu olan bir blokta tanımlanmıştır Razor .</span><span class="sxs-lookup"><span data-stu-id="73ffc-171">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="73ffc-172">Blazor Şablonlar, Razor Bu yaklaşımı kullanarak bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-172">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="73ffc-173">C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-173">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="73ffc-174">Aşağıdaki örnek, `Counter` [`@code`][1] bir şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir Blazor .</span><span class="sxs-lookup"><span data-stu-id="73ffc-174">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="73ffc-175">HTML Markup, Razor Code ve C# kodu aynı dosyada:</span><span class="sxs-lookup"><span data-stu-id="73ffc-175">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="73ffc-176">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-176">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="73ffc-177">`Counter`Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-177">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="73ffc-178">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-178">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="73ffc-179">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-179">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
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

<span data-ttu-id="73ffc-180">Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73ffc-180">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="73ffc-181">Bileşenler tarafından kullanılan tipik ad alanları Razor şunlardır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-181">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="73ffc-182">[`@using`][2]`_Imports.razor`dosyadaki yönergeler Razor `.razor` , C# dosyalarına () değil, yalnızca dosyalar () için geçerlidir `.cs` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-182">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="73ffc-183">Temel sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="73ffc-183">Specify a base class</span></span>

<span data-ttu-id="73ffc-184">[`@inherits`][6]Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-184">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="73ffc-185">Aşağıdaki örnek, bileşenin `BlazorRocksBase` özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel sınıfı nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-185">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="73ffc-186">Taban sınıfın türevi olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="73ffc-186">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="73ffc-187">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-187">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="73ffc-188">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-188">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="73ffc-189">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="73ffc-189">Use components</span></span>

<span data-ttu-id="73ffc-190">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-190">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="73ffc-191">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="73ffc-191">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="73ffc-192">İçinde aşağıdaki biçimlendirme `Pages/Index.razor` bir örneği işler `HeadingComponent` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-192">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="73ffc-193">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-193">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="73ffc-194">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-194">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="73ffc-195">[`@using`][2]Bileşenin ad alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-195">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="73ffc-196">Parametreler</span><span class="sxs-lookup"><span data-stu-id="73ffc-196">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="73ffc-197">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="73ffc-197">Route parameters</span></span>

<span data-ttu-id="73ffc-198">Bileşenler, yönergede belirtilen yol şablonundan rota parametreleri alabilir [`@page`][9] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-198">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="73ffc-199">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-199">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="73ffc-200">İsteğe bağlı parametreler desteklenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-200">Optional parameters are supported.</span></span> <span data-ttu-id="73ffc-201">Aşağıdaki örnekte, `text` isteğe bağlı parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-201">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="73ffc-202">Segment yoksa, değeri `Text` olarak ayarlanır `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-202">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="73ffc-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="73ffc-204">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-204">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="73ffc-205">İsteğe bağlı parametreler desteklenmez, bu nedenle [`@page`][9] Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-205">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="73ffc-206">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-206">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="73ffc-207">İkinci [`@page`][9] yönerge, `{text}` route parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-207">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="73ffc-208">`{*pageRoute}`Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri () hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/routing#catch-all-route-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="73ffc-208">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="73ffc-209">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="73ffc-209">Component parameters</span></span>

<span data-ttu-id="73ffc-210">Bileşenler, bileşen sınıfında özniteliğiyle ortak basit veya karmaşık özellikler kullanılarak tanımlanan *bileşen parametrelerine* sahip olabilir [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="73ffc-210">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="73ffc-211">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-211">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="73ffc-212">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-212">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="73ffc-213">Bileşen parametrelerine varsayılan bir değer atanabilir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-213">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="73ffc-214">Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` öğesinin özelliğinin değerini ayarlar `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-214">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="73ffc-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-215">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="73ffc-216">Kural gereği, C# kodundan oluşan bir öznitelik değeri, [ Razor ayrılmış `@` simgesi](xref:mvc/views/razor#razor-syntax)kullanılarak bir parametreye atanır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-216">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="73ffc-217">Üst alan veya özellik: `Title="@{FIELD OR PROPERTY}` yer tutucunun `{FIELD OR PROPERTY}` bir C# alanı veya üst bileşenin özelliği olduğu.</span><span class="sxs-lookup"><span data-stu-id="73ffc-217">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="73ffc-218">Bir yöntemin sonucu: `Title="@{METHOD}"` yer tutucunun, `{METHOD}` üst bileşenin C# yöntemi olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-218">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="73ffc-219">[Örtük veya açık ifade](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"` , yer tutucu `{EXPRESSION}` bir C# ifadesi.</span><span class="sxs-lookup"><span data-stu-id="73ffc-219">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="73ffc-220">Daha fazla bilgi için bkz. [ Razor ASP.NET Core için sözdizimi başvurusu](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="73ffc-220">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="73ffc-221">Kendi *bileşen parametrelerine* yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-221">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="73ffc-222">Daha fazla bilgi için, [üzerine yazılan parametreler](#overwritten-parameters) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-222">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="73ffc-223">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="73ffc-223">Child content</span></span>

<span data-ttu-id="73ffc-224">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-224">Components can set the content of another component.</span></span> <span data-ttu-id="73ffc-225">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-225">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="73ffc-226">Aşağıdaki örnekte, öğesinin `ChildComponent` `ChildContent` <xref:Microsoft.AspNetCore.Components.RenderFragment> işlemek için bir kullanıcı arabirimi segmentini temsil eden öğesini temsil eden bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-226">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="73ffc-227">Değeri, `ChildContent` bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-227">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="73ffc-228">Değeri, `ChildContent` ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-228">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="73ffc-229">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-229">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="73ffc-230">İçeriği alan özelliğin <xref:Microsoft.AspNetCore.Components.RenderFragment> `ChildContent` kural tarafından adlandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-230">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="73ffc-231">`ParentComponent`Örnek uygulamadaki ' de `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz `<ChildComponent>` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-231">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="73ffc-232">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-232">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="73ffc-233">Alt Blazor bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa, alt içeriği işleyen bir döngü içindeki işleme bileşenleri `for` yerel bir dizin değişkeni gerektirir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-233">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="73ffc-234">Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="73ffc-234">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="73ffc-235">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="73ffc-235">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="73ffc-236">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-236">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="73ffc-237">Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* [`@attributes`][3] Razor .</span><span class="sxs-lookup"><span data-stu-id="73ffc-237">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="73ffc-238">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-238">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="73ffc-239">Örneğin, `<input>` çok sayıda parametreyi destekleyen bir için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-239">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="73ffc-240">Aşağıdaki örnekte, ilk `<input>` öğesi ( `id="useIndividualParams"` ) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe ( `id="useAttributesDict"` ) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-240">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

<span data-ttu-id="73ffc-241">Parametrenin türü, `IEnumerable<KeyValuePair<string, object>>` dize anahtarları uygulamalıdır veya kullanmalıdır `IReadOnlyDictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-241">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="73ffc-242">`<input>`Her iki yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-242">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="73ffc-243">Rastgele öznitelikleri kabul etmek için [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) özelliği olarak ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-243">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="73ffc-244"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Üzerindeki özelliği, [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-244">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="73ffc-245">Bir bileşen yalnızca ile tek bir parametre tanımlayabilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="73ffc-245">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="73ffc-246">İle kullanılan özellik türü <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `Dictionary<string, object>` dize anahtarlarıyla atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-246">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="73ffc-247">`IEnumerable<KeyValuePair<string, object>>``IReadOnlyDictionary<string, object>`Ayrıca, Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-247">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="73ffc-248">[`@attributes`][3]Öğe özniteliklerinin konumuna göreli konumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-248">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="73ffc-249">Öğe üzerinde ne zaman bırakıldığında [`@attributes`][3] , öznitelikler sağdan sola (son-ilk) işlenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-249">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="73ffc-250">Bir bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun `Child` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-250">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="73ffc-251">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-251">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="73ffc-252">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-252">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="73ffc-253">`Child`Bileşenin `extra` özniteliği öğesinin sağına ayarlanır [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-253">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="73ffc-254">`Parent` `<div>` `extra="5"` Öznitelikler sağdan sola (en son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-254">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="73ffc-255">Aşağıdaki örnekte, `extra` ve sırası [`@attributes`][3] `Child` bileşen tarafından tersine çevrilir `<div>` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-255">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="73ffc-256">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-256">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="73ffc-257">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="73ffc-257">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="73ffc-258">Bileşendeki işlenen `<div>` `Parent` `extra="10"` ek öznitelik ile geçirildiğinde şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-258">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="73ffc-259">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="73ffc-259">Capture references to components</span></span>

<span data-ttu-id="73ffc-260">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya gibi komutları bu örneğe verebilirsiniz `Show` `Reset` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-260">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="73ffc-261">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="73ffc-261">To capture a component reference:</span></span>

* <span data-ttu-id="73ffc-262">[`@ref`][4]Alt bileşene bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73ffc-262">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="73ffc-263">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-263">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="73ffc-264">Bileşen işlendiğinde, `loginDialog` alan `CustomLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-264">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="73ffc-265">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-265">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73ffc-266">`loginDialog`Değişken yalnızca bileşen işlendikten sonra ve çıktısı öğesi içerdiğinde doldurulur `MyLoginDialog` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-266">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="73ffc-267">Bileşen işlenene kadar, başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="73ffc-267">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="73ffc-268">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için, [ `OnAfterRenderAsync` veya `OnAfterRender` yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-268">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="73ffc-269">Bir olay işleyicisiyle bir başvuru değişkeni kullanmak için bir lambda ifadesi kullanın veya [ `OnAfterRenderAsync` ya da `OnAfterRender` yöntemlerinde](xref:blazor/components/lifecycle#after-component-render)olay işleyicisi temsilcisini atayın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-269">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="73ffc-270">Bu, başvuru değişkeninin olay işleyicisi atanmadan önce atanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-270">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="73ffc-271">Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="73ffc-271">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="73ffc-272">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-272">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="73ffc-273">Bileşen başvuruları JavaScript koduna geçirilmiyor.</span><span class="sxs-lookup"><span data-stu-id="73ffc-273">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="73ffc-274">Bileşen başvuruları yalnızca .NET kodunda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-274">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="73ffc-275">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="73ffc-275">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="73ffc-276">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-276">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="73ffc-277">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-277">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="73ffc-278">Eşitleme bağlamı</span><span class="sxs-lookup"><span data-stu-id="73ffc-278">Synchronization context</span></span>

<span data-ttu-id="73ffc-279">Blazor<xref:System.Threading.SynchronizationContext>yürütmenin tek bir mantıksal iş parçacığını zorlamak için bir eşitleme bağlamı () kullanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-279">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="73ffc-280">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/components/lifecycle) ve tarafından oluşturulan tüm olay geri çağırmaları Blazor eşitleme bağlamında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="73ffc-280">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="73ffc-281">Blazor Server, tek iş parçacıklı bir ortamı öykünmeye çalışır ve bu sayede tek iş parçacıklı bir tarayıcıda WebAssembly modeliyle yakından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-281">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="73ffc-282">Belirli bir zamanda, iş, tek bir mantıksal iş parçacığının izlenimi vererek tam olarak bir iş parçacığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-282">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="73ffc-283">Aynı anda iki işlem yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-283">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="73ffc-284">İş parçacığı engelleme çağrılarını önleyin</span><span class="sxs-lookup"><span data-stu-id="73ffc-284">Avoid thread-blocking calls</span></span>

<span data-ttu-id="73ffc-285">Genellikle, aşağıdaki yöntemleri çağırmayın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-285">Generally, don't call the following methods.</span></span> <span data-ttu-id="73ffc-286">Aşağıdaki yöntemler iş parçacığını engeller ve bu nedenle, temel tamamlanana kadar uygulamanın çalışmaya devam ettirilmesi engellenir <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="73ffc-286">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="73ffc-287">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="73ffc-287">Invoke component methods externally to update state</span></span>

<span data-ttu-id="73ffc-288">Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi bir dış olay temel alınarak güncellenmesi gerekir, bu `InvokeAsync` yöntemi, Blazor eşitleme bağlamına dağımakta olan yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-288">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="73ffc-289">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="73ffc-289">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="73ffc-290">Şunu kaydedin `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-290">Register the `NotifierService`:</span></span>

* <span data-ttu-id="73ffc-291">İçinde Blazor WebAssembly , hizmeti şu şekilde ayrı kaydedin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-291">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="73ffc-292">' De Blazor Server , hizmeti kapsamında şu şekilde kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-292">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="73ffc-293">`NotifierService`Bir bileşeni güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="73ffc-293">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="73ffc-294">Önceki örnekte, `NotifierService` bileşen `OnNotify` metodunu Blazor eşitleme bağlamı dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-294">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="73ffc-295">`InvokeAsync` doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-295">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="73ffc-296">Daha fazla bilgi için bkz. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="73ffc-296">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="73ffc-297">\@Öğe ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="73ffc-297">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="73ffc-298">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor Bu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-298">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="73ffc-299">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-299">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="73ffc-300">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="73ffc-300">Consider the following example:</span></span>

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

<span data-ttu-id="73ffc-301">Koleksiyonun içeriği, `People` ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-301">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="73ffc-302">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı parametre değerleri almak için değişebilir `Details` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-302">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="73ffc-303">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-303">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="73ffc-304">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-304">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="73ffc-305">Eşleme işlemi, [`@key`][5] Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-305">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="73ffc-306">[`@key`][5] , anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="73ffc-306">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="73ffc-307">`People`Koleksiyon değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekler ve örnekler arasındaki ilişkilendirmeyi korur `person` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-307">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="73ffc-308">Bir, `Person` `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-308">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="73ffc-309">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-309">Other instances are left unchanged.</span></span>
* <span data-ttu-id="73ffc-310">Listedeki bir `Person` konuma eklenirse, `<DetailsEditor>` ilgili konuma bir yeni örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-310">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="73ffc-311">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-311">Other instances are left unchanged.</span></span>
* <span data-ttu-id="73ffc-312">`Person`Girişler yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-312">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="73ffc-313">Bazı senaryolarda, kullanımı [`@key`][5] rerendering karmaşıklığını en aza indirir ve odak konumu gıbı Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-313">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73ffc-314">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-314">Keys are local to each container element or component.</span></span> <span data-ttu-id="73ffc-315">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-315">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="73ffc-316">Anahtar ne zaman kullanılır? \@</span><span class="sxs-lookup"><span data-stu-id="73ffc-316">When to use \@key</span></span>

<span data-ttu-id="73ffc-317">Genellikle, [`@key`][5] her bir liste işlendiğinde (örneğin, bir [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) bloğunda) ve tanımlamak için uygun bir değer varsa, bu durum kullanılır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-317">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="73ffc-318">Bir [`@key`][5] Blazor nesne değiştiğinde bir öğeyi veya bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="73ffc-318">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="73ffc-319">`@currentPerson`Değişiklik olursa, [`@key`][5] öznitelik yönergesi Blazor tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-319">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="73ffc-320">Değişiklik sırasında hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-320">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="73ffc-321">Anahtar ne zaman kullanılmaz? \@</span><span class="sxs-lookup"><span data-stu-id="73ffc-321">When not to use \@key</span></span>

<span data-ttu-id="73ffc-322">İle yayılma yaparken bir performans maliyeti vardır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-322">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="73ffc-323">Performans maliyeti büyük değildir, ancak yalnızca [`@key`][5] öğenin veya bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="73ffc-323">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="73ffc-324">[`@key`][5]Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-324">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="73ffc-325">Kullanmanın tek avantajı [`@key`][5] model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-325">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="73ffc-326">Anahtar için kullanılacak değerler \@</span><span class="sxs-lookup"><span data-stu-id="73ffc-326">What values to use for \@key</span></span>

<span data-ttu-id="73ffc-327">Genellikle, için aşağıdaki değer türlerinden birini sağlamak mantıklı olur [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="73ffc-327">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="73ffc-328">Model nesne örnekleri (örneğin, `Person` Önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="73ffc-328">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="73ffc-329">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-329">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="73ffc-330">Benzersiz tanımlayıcılar (örneğin, veya türündeki birincil anahtar değerleri `int` `string` `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="73ffc-330">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="73ffc-331">Bu değerlerin çakışmayın için kullanıldığından emin olun [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-331">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="73ffc-332">Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-332">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="73ffc-333">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-333">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="73ffc-334">Üzerine yazılan parametreler</span><span class="sxs-lookup"><span data-stu-id="73ffc-334">Overwritten parameters</span></span>

<span data-ttu-id="73ffc-335">BlazorFramework genellikle güvenli üst-alt parametre ataması uygular:</span><span class="sxs-lookup"><span data-stu-id="73ffc-335">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="73ffc-336">Parametrelerin üzerine yazılması beklenmedik.</span><span class="sxs-lookup"><span data-stu-id="73ffc-336">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="73ffc-337">Yan etkiler en aza indirilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-337">Side-effects are minimized.</span></span> <span data-ttu-id="73ffc-338">Örneğin, sonsuz işleme döngüleri oluşturabileceğinden ek işlemeye kaçınılmaz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-338">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="73ffc-339">Alt bileşen, üst bileşen yeniden oluşturulduğunda varolan değerlerin üzerine yazılacak yeni parametre değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="73ffc-339">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="73ffc-340">Bir alt bileşendeki parametre değerlerinin üzerine yazılması, genellikle bileşeni bir veya daha fazla veriye dayalı parametre ile geliştirilirken ve geliştirici alt öğe içindeki bir parametreye doğrudan yazdığında oluşur:</span><span class="sxs-lookup"><span data-stu-id="73ffc-340">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="73ffc-341">Alt bileşen, üst bileşenden bir veya daha fazla parametre değeri ile işlenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-341">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="73ffc-342">Alt öğe doğrudan bir parametre değerine yazar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-342">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="73ffc-343">Üst bileşen, alt öğenin parametresinin değerini yeniden ekler ve üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-343">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="73ffc-344">Parametre değerlerinin üzerine yazılması olasılığı, alt bileşenin Özellik ayarlayıcılarına de göre genişletilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-344">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="73ffc-345">**Genel kılavuzumuzdan kendi parametrelerine doğrudan yazılan bileşenler oluşturmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="73ffc-345">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="73ffc-346">Aşağıdaki hatalı bileşeni göz önünde bulundurun `Expander` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-346">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="73ffc-347">Alt içeriği işler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-347">Renders child content.</span></span>
* <span data-ttu-id="73ffc-348">Bileşen parametresi () ile alt öğe içeriğini gösterir `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-348">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="73ffc-349">Bileşen doğrudan `Expanded` parametresine yazar, bu, üzerine yazılan parametrelerle ilgili sorunu gösterir ve kaçınılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-349">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
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

<span data-ttu-id="73ffc-350">`Expander`Bileşen, çağıraetkileyebilecek bir üst bileşene eklenir <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="73ffc-350">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="73ffc-351">Başlangıçta, `Expander` bileşenleri özellikleri bir kez değiştiğinde bağımsız olarak davranır `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-351">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="73ffc-352">Alt bileşenler, durumlarını beklendiği gibi korur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-352">The child components maintain their states as expected.</span></span> <span data-ttu-id="73ffc-353"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Üst öğede çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değeri () olarak döndürülür `true` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-353">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="73ffc-354">İkinci `Expander` `Expanded` bileşende hiçbir alt içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.</span><span class="sxs-lookup"><span data-stu-id="73ffc-354">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="73ffc-355">Önceki senaryodaki durumu korumak için bileşen içindeki *özel bir alanı* kullanarak, onun geçiş `Expander` durumunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="73ffc-355">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="73ffc-356">Aşağıdaki düzeltilen `Expander` bileşen:</span><span class="sxs-lookup"><span data-stu-id="73ffc-356">The following revised `Expander` component:</span></span>

* <span data-ttu-id="73ffc-357">Üst öğeden `Expanded` bileşen parametre değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="73ffc-357">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="73ffc-358"> `expanded` [OnInitialized olaydaki](xref:blazor/components/lifecycle#component-initialization-methods)bir özel alana () bileşen parametre değerini atar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-358">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="73ffc-359">Kendi iç geçiş durumunu korumak için özel alanını kullanır, bu da doğrudan bir parametreye yazmayı nasıl önleyeceğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-359">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

<span data-ttu-id="73ffc-360">Daha fazla bilgi için bkz. [ Blazor Iki yönlü bağlama hatası (DotNet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="73ffc-360">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="73ffc-361">Öznitelik uygulama</span><span class="sxs-lookup"><span data-stu-id="73ffc-361">Apply an attribute</span></span>

<span data-ttu-id="73ffc-362">Öznitelikler Razor , yönergeyle birlikte bileşenlere uygulanabilir [`@attribute`][7] .</span><span class="sxs-lookup"><span data-stu-id="73ffc-362">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="73ffc-363">Aşağıdaki örnek, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşen sınıfına özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="73ffc-363">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="73ffc-364">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="73ffc-364">Conditional HTML element attributes</span></span>

<span data-ttu-id="73ffc-365">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-365">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="73ffc-366">Değer `false` veya ise `null` , öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-366">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="73ffc-367">Değer ise `true` , öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-367">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="73ffc-368">Aşağıdaki örnekte, `IsCompleted` `checked` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini belirler:</span><span class="sxs-lookup"><span data-stu-id="73ffc-368">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="73ffc-369">`IsCompleted`İse `true` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-369">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="73ffc-370">`IsCompleted`İse `false` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-370">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="73ffc-371">Daha fazla bilgi için bkz. [ Razor ASP.NET Core için sözdizimi başvurusu](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="73ffc-371">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="73ffc-372">[`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons).NET türü bir olduğunda, gibi bazı HTML öznitelikleri düzgün şekilde çalışmaz `bool` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-372">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="73ffc-373">Bu durumlarda, `string` yerine bir tür kullanın `bool` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-373">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="73ffc-374">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="73ffc-374">Raw HTML</span></span>

<span data-ttu-id="73ffc-375">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-375">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="73ffc-376">Ham HTML işlemek için, HTML içeriğini bir değerde sarın `MarkupString` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-376">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="73ffc-377">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-377">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="73ffc-378">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="73ffc-378">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="73ffc-379">Aşağıdaki örnek, `MarkupString` bir bileşenin işlenmiş çıktısına STATIK HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="73ffc-379">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="73ffc-380">Razor şablondan</span><span class="sxs-lookup"><span data-stu-id="73ffc-380">Razor templates</span></span>

<span data-ttu-id="73ffc-381">İşleme parçaları, Razor şablon sözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-381">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="73ffc-382">Razor Şablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="73ffc-382">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="73ffc-383">Aşağıdaki örnek, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> bir bileşeni doğrudan bir bileşende nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-383">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="73ffc-384">Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/components/templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-384">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="73ffc-385">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="73ffc-385">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="73ffc-386">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="73ffc-386">Static assets</span></span>

<span data-ttu-id="73ffc-387">BlazorProjenin [ `web root (wwwroot)` klasörü](xref:fundamentals/index#web-root)altına statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.</span><span class="sxs-lookup"><span data-stu-id="73ffc-387">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="73ffc-388">`/`Statik bir varlık için Web köküne başvurmak üzere temel göreli bir yol () kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-388">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="73ffc-389">Aşağıdaki örnekte, bu `logo.png` klasörde fiziksel olarak bulunur `{PROJECT ROOT}/wwwroot/images` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-389">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="73ffc-390">Razorbileşenler,  tilde işareti gösterimini ( `~/` ) desteklemez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-390">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="73ffc-391">Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="73ffc-391">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="73ffc-392">Etiket Yardımcıları bileşenlerde desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="73ffc-392">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="73ffc-393">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)Razorbileşenlerde ( `.razor` Dosyalar) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-393">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="73ffc-394">' De etiket Yardımcısı benzeri işlevsellik sağlamak için Blazor , etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-394">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="73ffc-395">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="73ffc-395">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="73ffc-396">İşleme Blazor HTML, ölçeklenebilir vektör grafik (SVG) görüntüleri de dahil olmak üzere tarayıcıda desteklenen görüntüler ( `.svg` ) etiketi aracılığıyla desteklenir `<img>` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-396">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="73ffc-397">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının () CSS kurallarında desteklenir `.css` :</span><span class="sxs-lookup"><span data-stu-id="73ffc-397">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="73ffc-398">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-398">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="73ffc-399">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına ( `.razor` ) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-399">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="73ffc-400">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve [`@bind`][10] bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-400">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="73ffc-401">Daha fazla bilgi için bkz. [Içindeki SVG desteği Blazor (DotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="73ffc-401">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="73ffc-402">Boşluk işleme davranışı</span><span class="sxs-lookup"><span data-stu-id="73ffc-402">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="73ffc-403">[`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace)Yönergesi bir değeriyle birlikte kullanılmamışsa `true` , şu durumlarda fazladan boşluk varsayılan olarak kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="73ffc-403">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="73ffc-404">Bir öğe içinde baştaki veya sondaki.</span><span class="sxs-lookup"><span data-stu-id="73ffc-404">Leading or trailing within an element.</span></span>
* <span data-ttu-id="73ffc-405">Bir parametre içinde baştaki veya sondaki `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-405">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="73ffc-406">Örneğin, alt içerik başka bir bileşene geçirildi.</span><span class="sxs-lookup"><span data-stu-id="73ffc-406">For example, child content passed to another component.</span></span>
* <span data-ttu-id="73ffc-407">Ya da gibi bir C# kod bloğunun önünde veya sonrasında gelir `@if` `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-407">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="73ffc-408">Boşluk kaldırma, bir CSS kuralı kullanılırken işlenen çıktıyı etkileyebilir `white-space: pre` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-408">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="73ffc-409">Bu performans iyileştirmesini devre dışı bırakmak ve boşluğu korumak için aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="73ffc-409">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="73ffc-410">`@preservewhitespace true` `.razor` Tercihi belirli bir bileşene uygulamak için dosyanın en üstüne yönergesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73ffc-410">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="73ffc-411">`@preservewhitespace true`Bir `_Imports.razor` alt dizine veya tüm projeye tercihi uygulamak için yönergesini bir dosyanın içine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73ffc-411">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="73ffc-412">Çoğu durumda, uygulamalar genellikle normal şekilde çalışmaya devam ederken (ancak daha hızlı), hiçbir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="73ffc-412">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="73ffc-413">Boşluğu çıkarmak belirli bir bileşen için herhangi bir soruna neden oluyorsa, `@preservewhitespace true` Bu iyileştirmeyi devre dışı bırakmak için o bileşende kullanın.</span><span class="sxs-lookup"><span data-stu-id="73ffc-413">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="73ffc-414">Boşluk, bileşenin kaynak kodunda tutulur.</span><span class="sxs-lookup"><span data-stu-id="73ffc-414">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="73ffc-415">Görsel bir efekt olmasa bile, yalnızca boşluk metni tarayıcı Belge Nesne Modeli (DOM) içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-415">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="73ffc-416">Aşağıdaki bileşen kodunu göz önünde bulundurun Razor :</span><span class="sxs-lookup"><span data-stu-id="73ffc-416">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="73ffc-417">Yukarıdaki örnek aşağıdaki gereksiz boşluğu işler:</span><span class="sxs-lookup"><span data-stu-id="73ffc-417">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="73ffc-418">`@foreach`Kod bloğunun dışında.</span><span class="sxs-lookup"><span data-stu-id="73ffc-418">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="73ffc-419">Öğesinin etrafında `<li>` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-419">Around the `<li>` element.</span></span>
* <span data-ttu-id="73ffc-420">Çıktı etrafında `@item.Text` .</span><span class="sxs-lookup"><span data-stu-id="73ffc-420">Around the `@item.Text` output.</span></span>

<span data-ttu-id="73ffc-421">100 öğe içeren bir liste, boşluk olan 402 alan ile sonuçlanır ve ek boşluklardan hiçbiri işlenmiş çıktıyı görsel olarak etkiler.</span><span class="sxs-lookup"><span data-stu-id="73ffc-421">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="73ffc-422">Bileşenler için statik HTML işlenirken, bir etiketin içindeki boşluk korunmaz.</span><span class="sxs-lookup"><span data-stu-id="73ffc-422">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="73ffc-423">Örneğin, işlenen çıktıda aşağıdaki bileşenin kaynağını görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="73ffc-423">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="73ffc-424">Yukarıdaki biçimlendirmeden boşluk korunmaz Razor :</span><span class="sxs-lookup"><span data-stu-id="73ffc-424">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="73ffc-425">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="73ffc-425">Additional resources</span></span>

* <span data-ttu-id="73ffc-426"><xref:blazor/security/server/threat-mitigation>: Blazor Server Kaynak tükenmesi ile Çekişmek zorunda olan uygulamalar oluşturmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="73ffc-426"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[2]: <xref:mvc/views/razor#using> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[3]: <xref:mvc/views/razor#attributes> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[4]: <xref:mvc/views/razor#ref> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[5]: <xref:mvc/views/razor#key> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[6]: <xref:mvc/views/razor#inherits> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[7]: <xref:mvc/views/razor#attribute> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[8]: <xref:mvc/views/razor#namespace> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[9]: <xref:mvc/views/razor#page> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
[10]: <xref:mvc/views/razor#bind> "::: No-Loc (Razor)::: ASP.NET Core için sözdizimi başvurusu"
