---
title: 'ASP.NET Core bileşenleri oluşturma ve kullanma :::no-loc(Razor):::'
author: guardrex
description: :::no-loc(Razor):::Verileri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/components/index
ms.openlocfilehash: d8838a458943599890420adec4551ad87e43d328
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637710"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="12010-103">ASP.NET Core bileşenleri oluşturma ve kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="12010-103">Create and use ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="12010-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="12010-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="12010-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12010-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="12010-106">:::no-loc(Blazor)::: uygulamalar, *Bileşenler* kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="12010-106">:::no-loc(Blazor)::: apps are built using *components*.</span></span> <span data-ttu-id="12010-107">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="12010-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="12010-108">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="12010-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="12010-109">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="12010-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="12010-110">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="12010-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="12010-111">Component classes</span></span>

<span data-ttu-id="12010-112">Bileşenler, [:::no-loc(Razor):::](xref:mvc/views/razor) `.razor` C# ve HTML biçimlendirmesinin bir birleşimi kullanılarak bileşen dosyalarında () uygulanır.</span><span class="sxs-lookup"><span data-stu-id="12010-112">Components are implemented in [:::no-loc(Razor):::](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="12010-113">İçindeki bir bileşen :::no-loc(Blazor)::: bir *:::no-loc(Razor)::: bileşen* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="12010-113">A component in :::no-loc(Blazor)::: is formally referred to as a *:::no-loc(Razor)::: component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="12010-114">:::no-loc(Razor)::: sözdizimi</span><span class="sxs-lookup"><span data-stu-id="12010-114">:::no-loc(Razor)::: syntax</span></span>

<span data-ttu-id="12010-115">:::no-loc(Razor)::: uygulamalardaki bileşenler :::no-loc(Blazor)::: yaygın olarak :::no-loc(Razor)::: söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="12010-115">:::no-loc(Razor)::: components in :::no-loc(Blazor)::: apps extensively use :::no-loc(Razor)::: syntax.</span></span> <span data-ttu-id="12010-116">:::no-loc(Razor):::Biçimlendirme diline alışkın değilseniz, devam etmeden önce okumanız önerilir <xref:mvc/views/razor> .</span><span class="sxs-lookup"><span data-stu-id="12010-116">If you aren't familiar with the :::no-loc(Razor)::: markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="12010-117">Söz dizimi üzerindeki içeriğe erişirken :::no-loc(Razor)::: , aşağıdaki bölümlere özel bir dikkat ödeyin:</span><span class="sxs-lookup"><span data-stu-id="12010-117">When accessing the content on :::no-loc(Razor)::: syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="12010-118">[Yönergeler](xref:mvc/views/razor#directives): `@` -genellikle bileşen biçimlendirmesinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="12010-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="12010-119">[Yönerge öznitelikleri](xref:mvc/views/razor#directive-attributes): `@` -önek olarak bileşen öğelerinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.</span><span class="sxs-lookup"><span data-stu-id="12010-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="12010-120">Adlar</span><span class="sxs-lookup"><span data-stu-id="12010-120">Names</span></span>

<span data-ttu-id="12010-121">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="12010-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="12010-122">Örneğin, `MyCoolComponent.razor` geçerlidir ve `myCoolComponent.razor` geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="12010-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="12010-123">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="12010-123">Routing</span></span>

<span data-ttu-id="12010-124">Uygulamasında yönlendirme, :::no-loc(Blazor)::: uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="12010-124">Routing in :::no-loc(Blazor)::: is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="12010-125">:::no-loc(Razor)::: [`@page`][9] Yönergeyle bir dosya derlendiğinde, oluşturulan sınıfa <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> yol şablonunu belirten bir değer verilir.</span><span class="sxs-lookup"><span data-stu-id="12010-125">When a :::no-loc(Razor)::: file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="12010-126">Çalışma zamanında, yönlendirici bileşen sınıflarını bir ile arar <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ve hangi bileşenin Istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="12010-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="12010-127">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="12010-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="12010-128">İşaretleme</span><span class="sxs-lookup"><span data-stu-id="12010-128">Markup</span></span>

<span data-ttu-id="12010-129">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="12010-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="12010-130">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir *:::no-loc(Razor):::* .</span><span class="sxs-lookup"><span data-stu-id="12010-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *:::no-loc(Razor):::*.</span></span> <span data-ttu-id="12010-131">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="12010-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="12010-132">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="12010-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="12010-133">Bileşen sınıfının üyeleri bir [`@code`][1] blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="12010-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="12010-134">[`@code`][1]Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="12010-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="12010-135">Birden çok [`@code`][1] blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="12010-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="12010-136">Bileşen üyeleri, ile başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir `@` .</span><span class="sxs-lookup"><span data-stu-id="12010-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="12010-137">Örneğin, bir C# alanı alan adının önüne eklenerek işlenir `@` .</span><span class="sxs-lookup"><span data-stu-id="12010-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="12010-138">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="12010-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="12010-139">`headingFontStyle` için CSS özellik değerine `font-style` .</span><span class="sxs-lookup"><span data-stu-id="12010-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="12010-140">`headingText``<h1>`öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="12010-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new :::no-loc(Blazor):::!";
}
```

<span data-ttu-id="12010-141">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="12010-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="12010-142">:::no-loc(Blazor)::: ardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.</span><span class="sxs-lookup"><span data-stu-id="12010-142">:::no-loc(Blazor)::: then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="12010-143">Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="12010-144">Web sayfalarını üreten bileşenler genellikle `Pages` klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="12010-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="12010-145">Sayfa olmayan bileşenler sıklıkla `Shared` klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="12010-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="12010-146">Ad alanları</span><span class="sxs-lookup"><span data-stu-id="12010-146">Namespaces</span></span>

<span data-ttu-id="12010-147">Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir.</span><span class="sxs-lookup"><span data-stu-id="12010-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="12010-148">Uygulamanın kök ad alanı ise `:::no-loc(Blazor):::Sample` ve `Counter` bileşen `Pages` klasöründe bulunuyorsa:</span><span class="sxs-lookup"><span data-stu-id="12010-148">If the app's root namespace is `:::no-loc(Blazor):::Sample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="12010-149">`Counter`Bileşenin ad alanı `:::no-loc(Blazor):::Sample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="12010-149">The `Counter` component's namespace is `:::no-loc(Blazor):::Sample.Pages`.</span></span>
* <span data-ttu-id="12010-150">Bileşenin tam nitelikli tür adı `:::no-loc(Blazor):::Sample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="12010-150">The fully qualified type name of the component is `:::no-loc(Blazor):::Sample.Pages.Counter`.</span></span>

<span data-ttu-id="12010-151">Bileşenleri tutan özel klasörler için, [`@using`][2] üst bileşene veya uygulamanın dosyasına bir yönerge ekleyin `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="12010-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="12010-152">Aşağıdaki örnek, klasördeki bileşenleri kullanılabilir hale getirir `Components` :</span><span class="sxs-lookup"><span data-stu-id="12010-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using :::no-loc(Blazor):::Sample.Components
```

<span data-ttu-id="12010-153">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`][2] yönergeyi gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="12010-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<:::no-loc(Blazor):::Sample.Components.MyComponent />
```

<span data-ttu-id="12010-154">İle yazılmış bir bileşenin ad alanı, :::no-loc(Razor)::: tabanlıdır (öncelik sırasına göre):</span><span class="sxs-lookup"><span data-stu-id="12010-154">The namespace of a component authored with :::no-loc(Razor)::: is based on (in priority order):</span></span>

* <span data-ttu-id="12010-155">[`@namespace`][8]:::no-loc(Razor):::dosya ( `.razor` ) biçimlendirmesinde atama () `@namespace :::no-loc(Blazor):::Sample.MyNamespace` .</span><span class="sxs-lookup"><span data-stu-id="12010-155">[`@namespace`][8] designation in :::no-loc(Razor)::: file (`.razor`) markup (`@namespace :::no-loc(Blazor):::Sample.MyNamespace`).</span></span>
* <span data-ttu-id="12010-156">Proje, `RootNamespace` Proje dosyasında ( `<RootNamespace>:::no-loc(Blazor):::Sample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="12010-156">The project's `RootNamespace` in the project file (`<RootNamespace>:::no-loc(Blazor):::Sample</RootNamespace>`).</span></span>
* <span data-ttu-id="12010-157">Proje dosyasının dosya adından () alınan proje adı `.csproj` ve proje kökünden bileşen yolu.</span><span class="sxs-lookup"><span data-stu-id="12010-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="12010-158">Örneğin, çerçeve `{PROJECT ROOT}/Pages/Index.razor` `:::no-loc(Blazor):::Sample.csproj` ad alanına () çözümler `:::no-loc(Blazor):::Sample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="12010-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`:::no-loc(Blazor):::Sample.csproj`) to the namespace `:::no-loc(Blazor):::Sample.Pages`.</span></span> <span data-ttu-id="12010-159">Bileşenler C# ad bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="12010-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="12010-160">`Index`Bu örnekteki bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:</span><span class="sxs-lookup"><span data-stu-id="12010-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="12010-161">Aynı klasörde, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="12010-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="12010-162">Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.</span><span class="sxs-lookup"><span data-stu-id="12010-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="12010-163">`global::`Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="12010-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="12010-164">Diğer ad [`using`](/dotnet/csharp/language-reference/keywords/using-statement) deyimleri (örneğin,) ile bileşenleri içeri aktarma `@using Foo = Bar` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="12010-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="12010-165">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="12010-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="12010-166">Örneğin, `@using :::no-loc(Blazor):::Sample` `NavMenu` ile bileşeni () ekleme ve başvuru `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="12010-166">For example, adding `@using :::no-loc(Blazor):::Sample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="12010-167">Kısmi sınıf desteği</span><span class="sxs-lookup"><span data-stu-id="12010-167">Partial class support</span></span>

<span data-ttu-id="12010-168">:::no-loc(Razor)::: bileşenler kısmi sınıflar olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="12010-168">:::no-loc(Razor)::: components are generated as partial classes.</span></span> <span data-ttu-id="12010-169">:::no-loc(Razor)::: bileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="12010-169">:::no-loc(Razor)::: components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="12010-170">C# kodu, [`@code`][1] tek bir dosyada HTML işaretlemesi ve kodu olan bir blokta tanımlanmıştır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="12010-170">C# code is defined in an [`@code`][1] block with HTML markup and :::no-loc(Razor)::: code in a single file.</span></span> <span data-ttu-id="12010-171">:::no-loc(Blazor)::: Şablonlar, :::no-loc(Razor)::: Bu yaklaşımı kullanarak bileşenlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="12010-171">:::no-loc(Blazor)::: templates define their :::no-loc(Razor)::: components using this approach.</span></span>
* <span data-ttu-id="12010-172">C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="12010-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="12010-173">Aşağıdaki örnek, `Counter` [`@code`][1] bir şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="12010-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a :::no-loc(Blazor)::: template.</span></span> <span data-ttu-id="12010-174">HTML Markup, :::no-loc(Razor)::: Code ve C# kodu aynı dosyada:</span><span class="sxs-lookup"><span data-stu-id="12010-174">HTML markup, :::no-loc(Razor)::: code, and C# code are in the same file:</span></span>

<span data-ttu-id="12010-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="12010-176">`Counter`Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="12010-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="12010-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="12010-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="12010-178">`Counter.razor.cs`:</span></span>

```csharp
namespace :::no-loc(Blazor):::Sample.Pages
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

<span data-ttu-id="12010-179">Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin.</span><span class="sxs-lookup"><span data-stu-id="12010-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="12010-180">Bileşenler tarafından kullanılan tipik ad alanları :::no-loc(Razor)::: şunlardır:</span><span class="sxs-lookup"><span data-stu-id="12010-180">Typical namespaces used by :::no-loc(Razor)::: components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="12010-181">[`@using`][2]`_Imports.razor`dosyadaki yönergeler :::no-loc(Razor)::: `.razor` , C# dosyalarına () değil, yalnızca dosyalar () için geçerlidir `.cs` .</span><span class="sxs-lookup"><span data-stu-id="12010-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to :::no-loc(Razor)::: files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="12010-182">Temel sınıf belirtin</span><span class="sxs-lookup"><span data-stu-id="12010-182">Specify a base class</span></span>

<span data-ttu-id="12010-183">[`@inherits`][6]Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="12010-184">Aşağıdaki örnek, bileşenin `:::no-loc(Blazor):::RocksBase` özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel sınıfı nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="12010-184">The following example shows how a component can inherit a base class, `:::no-loc(Blazor):::RocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="12010-185">Taban sınıfın türevi olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="12010-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="12010-186">`Pages/:::no-loc(Blazor):::Rocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-186">`Pages/:::no-loc(Blazor):::Rocks.razor`:</span></span>

```razor
@page "/:::no-loc(Blazor):::Rocks"
@inherits :::no-loc(Blazor):::RocksBase

<h1>@:::no-loc(Blazor):::RocksText</h1>
```

<span data-ttu-id="12010-187">`:::no-loc(Blazor):::RocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="12010-187">`:::no-loc(Blazor):::RocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace :::no-loc(Blazor):::Sample
{
    public class :::no-loc(Blazor):::RocksBase : ComponentBase
    {
        public string :::no-loc(Blazor):::RocksText { get; set; } = 
            ":::no-loc(Blazor)::: rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="12010-188">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="12010-188">Use components</span></span>

<span data-ttu-id="12010-189">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="12010-190">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="12010-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="12010-191">İçinde aşağıdaki biçimlendirme `Pages/Index.razor` bir örneği işler `HeadingComponent` :</span><span class="sxs-lookup"><span data-stu-id="12010-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="12010-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="12010-193">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="12010-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="12010-194">[`@using`][2]Bileşenin ad alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="12010-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="12010-195">Parametreler</span><span class="sxs-lookup"><span data-stu-id="12010-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="12010-196">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="12010-196">Route parameters</span></span>

<span data-ttu-id="12010-197">Bileşenler, yönergede belirtilen yol şablonundan rota parametreleri alabilir [`@page`][9] .</span><span class="sxs-lookup"><span data-stu-id="12010-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="12010-198">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="12010-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12010-199">İsteğe bağlı parametreler desteklenir.</span><span class="sxs-lookup"><span data-stu-id="12010-199">Optional parameters are supported.</span></span> <span data-ttu-id="12010-200">Aşağıdaki örnekte, `text` isteğe bağlı parametresi, yol segmentinin değerini bileşenin `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="12010-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="12010-201">Segment yoksa, değeri `Text` olarak ayarlanır `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="12010-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="12010-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="12010-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="12010-204">İsteğe bağlı parametreler desteklenmez, bu nedenle [`@page`][9] Önceki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="12010-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="12010-205">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="12010-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="12010-206">İkinci [`@page`][9] yönerge, `{text}` route parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="12010-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="12010-207">`{*pageRoute}`Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri () hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/routing#catch-all-route-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="12010-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="12010-208">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="12010-208">Component parameters</span></span>

<span data-ttu-id="12010-209">Bileşenler, bileşen sınıfında özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine* sahip olabilir [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="12010-209">Components can have *component parameters* , which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="12010-210">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="12010-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="12010-212">Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` öğesinin özelliğinin değerini ayarlar `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="12010-212">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="12010-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-213">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="12010-214">Kural gereği, C# kodundan oluşan bir öznitelik değeri, [ :::no-loc(Razor)::: ayrılmış `@` simgesi](xref:mvc/views/razor#razor-syntax)kullanılarak bir parametreye atanır:</span><span class="sxs-lookup"><span data-stu-id="12010-214">By convention, an attribute value that consists of C# code is assigned to a parameter using [:::no-loc(Razor):::'s reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="12010-215">Üst alan veya özellik: `Title="@{FIELD OR PROPERTY}` yer tutucunun `{FIELD OR PROPERTY}` bir C# alanı veya üst bileşenin özelliği olduğu.</span><span class="sxs-lookup"><span data-stu-id="12010-215">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="12010-216">Bir yöntemin sonucu: `Title="@{METHOD}"` yer tutucunun, `{METHOD}` üst bileşenin C# yöntemi olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="12010-216">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="12010-217">[Örtük veya açık ifade](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"` , yer tutucu `{EXPRESSION}` bir C# ifadesi.</span><span class="sxs-lookup"><span data-stu-id="12010-217">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="12010-218">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="12010-218">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="12010-219">Kendi *bileşen parametrelerine* yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-219">Don't create components that write to their own *component parameters* , use a private field instead.</span></span> <span data-ttu-id="12010-220">Daha fazla bilgi için, [üzerine yazılan parametreler](#overwritten-parameters) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="12010-220">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="12010-221">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="12010-221">Child content</span></span>

<span data-ttu-id="12010-222">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-222">Components can set the content of another component.</span></span> <span data-ttu-id="12010-223">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="12010-223">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="12010-224">Aşağıdaki örnekte, öğesinin `ChildComponent` `ChildContent` <xref:Microsoft.AspNetCore.Components.RenderFragment> işlemek için bir kullanıcı arabirimi segmentini temsil eden öğesini temsil eden bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="12010-224">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="12010-225">Değeri, `ChildContent` bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="12010-225">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="12010-226">Değeri, `ChildContent` ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="12010-226">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="12010-227">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-227">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="12010-228">İçeriği alan özelliğin <xref:Microsoft.AspNetCore.Components.RenderFragment> `ChildContent` kural tarafından adlandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="12010-228">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="12010-229">`ParentComponent`Örnek uygulamadaki ' de `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz `<ChildComponent>` .</span><span class="sxs-lookup"><span data-stu-id="12010-229">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="12010-230">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-230">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="12010-231">Alt :::no-loc(Blazor)::: bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa, alt içeriği işleyen bir döngü içindeki işleme bileşenleri `for` yerel bir dizin değişkeni gerektirir:</span><span class="sxs-lookup"><span data-stu-id="12010-231">Due to the way that :::no-loc(Blazor)::: renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="12010-232">Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="12010-232">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="12010-233">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="12010-233">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="12010-234">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-234">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="12010-235">Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* [`@attributes`][3] :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="12010-235">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] :::no-loc(Razor)::: directive.</span></span> <span data-ttu-id="12010-236">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="12010-236">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="12010-237">Örneğin, `<input>` çok sayıda parametreyi destekleyen bir için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-237">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="12010-238">Aşağıdaki örnekte, ilk `<input>` öğesi ( `id="useIndividualParams"` ) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe ( `id="useAttributesDict"` ) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="12010-238">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="12010-239">Parametrenin türü, `IEnumerable<KeyValuePair<string, object>>` dize anahtarları uygulamalıdır veya kullanmalıdır `IReadOnlyDictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="12010-239">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="12010-240">`<input>`Her iki yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="12010-240">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="12010-241">Rastgele öznitelikleri kabul etmek için [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) özelliği olarak ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` :</span><span class="sxs-lookup"><span data-stu-id="12010-241">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="12010-242"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Üzerindeki özelliği, [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="12010-242">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="12010-243">Bir bileşen yalnızca ile tek bir parametre tanımlayabilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="12010-243">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="12010-244">İle kullanılan özellik türü <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `Dictionary<string, object>` dize anahtarlarıyla atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="12010-244">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="12010-245">`IEnumerable<KeyValuePair<string, object>>``IReadOnlyDictionary<string, object>`Ayrıca, Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="12010-245">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="12010-246">[`@attributes`][3]Öğe özniteliklerinin konumuna göreli konumu önemlidir.</span><span class="sxs-lookup"><span data-stu-id="12010-246">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="12010-247">Öğe üzerinde ne zaman bırakıldığında [`@attributes`][3] , öznitelikler sağdan sola (son-ilk) işlenir.</span><span class="sxs-lookup"><span data-stu-id="12010-247">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="12010-248">Bir bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun `Child` :</span><span class="sxs-lookup"><span data-stu-id="12010-248">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="12010-249">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-249">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="12010-250">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-250">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="12010-251">`Child`Bileşenin `extra` özniteliği öğesinin sağına ayarlanır [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="12010-251">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="12010-252">`Parent` `<div>` `extra="5"` Öznitelikler sağdan sola (en son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:</span><span class="sxs-lookup"><span data-stu-id="12010-252">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="12010-253">Aşağıdaki örnekte, `extra` ve sırası [`@attributes`][3] `Child` bileşen tarafından tersine çevrilir `<div>` :</span><span class="sxs-lookup"><span data-stu-id="12010-253">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="12010-254">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-254">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="12010-255">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12010-255">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="12010-256">Bileşendeki işlenen `<div>` `Parent` `extra="10"` ek öznitelik ile geçirildiğinde şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="12010-256">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="12010-257">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="12010-257">Capture references to components</span></span>

<span data-ttu-id="12010-258">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya gibi komutları bu örneğe verebilirsiniz `Show` `Reset` .</span><span class="sxs-lookup"><span data-stu-id="12010-258">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="12010-259">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="12010-259">To capture a component reference:</span></span>

* <span data-ttu-id="12010-260">[`@ref`][4]Alt bileşene bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="12010-260">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="12010-261">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="12010-261">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="12010-262">Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="12010-262">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="12010-263">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="12010-263">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="12010-264">`loginDialog`Değişken yalnızca bileşen işlendikten sonra ve çıktısı öğesi içerdiğinde doldurulur `MyLoginDialog` .</span><span class="sxs-lookup"><span data-stu-id="12010-264">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="12010-265">Bileşen işlenene kadar, başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="12010-265">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="12010-266">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için, [ `OnAfterRenderAsync` veya `OnAfterRender` yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-266">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="12010-267">Bir olay işleyicisiyle bir başvuru değişkeni kullanmak için bir lambda ifadesi kullanın veya [ `OnAfterRenderAsync` ya da `OnAfterRender` yöntemlerinde](xref:blazor/components/lifecycle#after-component-render)olay işleyicisi temsilcisini atayın.</span><span class="sxs-lookup"><span data-stu-id="12010-267">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="12010-268">Bu, başvuru değişkeninin olay işleyicisi atanmadan önce atanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="12010-268">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="12010-269">Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="12010-269">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="12010-270">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="12010-270">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="12010-271">Bileşen başvuruları JavaScript koduna geçirilmiyor.</span><span class="sxs-lookup"><span data-stu-id="12010-271">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="12010-272">Bileşen başvuruları yalnızca .NET kodunda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="12010-272">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="12010-273">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="12010-273">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="12010-274">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-274">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="12010-275">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="12010-275">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="12010-276">Eşitleme bağlamı</span><span class="sxs-lookup"><span data-stu-id="12010-276">Synchronization context</span></span>

<span data-ttu-id="12010-277">:::no-loc(Blazor):::<xref:System.Threading.SynchronizationContext>yürütmenin tek bir mantıksal iş parçacığını zorlamak için bir eşitleme bağlamı () kullanır.</span><span class="sxs-lookup"><span data-stu-id="12010-277">:::no-loc(Blazor)::: uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="12010-278">Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/components/lifecycle) ve tarafından oluşturulan tüm olay geri çağırmaları :::no-loc(Blazor)::: eşitleme bağlamında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="12010-278">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by :::no-loc(Blazor)::: are executed on the synchronization context.</span></span>

<span data-ttu-id="12010-279">:::no-loc(Blazor Server):::, tek iş parçacıklı bir ortamı öykünmeye çalışır ve bu sayede tek iş parçacıklı bir tarayıcıda WebAssembly modeliyle yakından eşleşir.</span><span class="sxs-lookup"><span data-stu-id="12010-279">:::no-loc(Blazor Server):::'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="12010-280">Belirli bir zamanda, iş, tek bir mantıksal iş parçacığının izlenimi vererek tam olarak bir iş parçacığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="12010-280">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="12010-281">Aynı anda iki işlem yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="12010-281">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="12010-282">İş parçacığı engelleme çağrılarını önleyin</span><span class="sxs-lookup"><span data-stu-id="12010-282">Avoid thread-blocking calls</span></span>

<span data-ttu-id="12010-283">Genellikle, aşağıdaki yöntemleri çağırmayın.</span><span class="sxs-lookup"><span data-stu-id="12010-283">Generally, don't call the following methods.</span></span> <span data-ttu-id="12010-284">Aşağıdaki yöntemler iş parçacığını engeller ve bu nedenle, temel tamamlanana kadar uygulamanın çalışmaya devam ettirilmesi engellenir <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="12010-284">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="12010-285">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="12010-285">Invoke component methods externally to update state</span></span>

<span data-ttu-id="12010-286">Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi bir dış olay temel alınarak güncellenmesi gerekir, bu `InvokeAsync` yöntemi, :::no-loc(Blazor)::: eşitleme bağlamına dağımakta olan yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-286">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to :::no-loc(Blazor):::'s synchronization context.</span></span> <span data-ttu-id="12010-287">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="12010-287">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="12010-288">Şunu kaydedin `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="12010-288">Register the `NotifierService`:</span></span>

* <span data-ttu-id="12010-289">İçinde :::no-loc(Blazor WebAssembly)::: , hizmeti şu şekilde ayrı kaydedin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="12010-289">In :::no-loc(Blazor WebAssembly):::, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="12010-290">' De :::no-loc(Blazor Server)::: , hizmeti kapsamında şu şekilde kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="12010-290">In :::no-loc(Blazor Server):::, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="12010-291">`NotifierService`Bir bileşeni güncelleştirmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="12010-291">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="12010-292">Önceki örnekte, `NotifierService` bileşen `OnNotify` metodunu :::no-loc(Blazor)::: eşitleme bağlamı dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="12010-292">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of :::no-loc(Blazor):::'s synchronization context.</span></span> <span data-ttu-id="12010-293">`InvokeAsync` doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="12010-293">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="12010-294">\@Öğe ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="12010-294">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="12010-295">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, :::no-loc(Blazor)::: Bu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="12010-295">When rendering a list of elements or components and the elements or components subsequently change, :::no-loc(Blazor):::'s diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="12010-296">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="12010-296">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="12010-297">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="12010-297">Consider the following example:</span></span>

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

<span data-ttu-id="12010-298">Koleksiyonun içeriği, `People` ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-298">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="12010-299">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı parametre değerleri almak için değişebilir `Details` .</span><span class="sxs-lookup"><span data-stu-id="12010-299">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="12010-300">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-300">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="12010-301">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-301">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="12010-302">Eşleme işlemi, [`@key`][5] Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-302">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="12010-303">[`@key`][5] , anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="12010-303">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="12010-304">`People`Koleksiyon değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekler ve örnekler arasındaki ilişkilendirmeyi korur `person` :</span><span class="sxs-lookup"><span data-stu-id="12010-304">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="12010-305">Bir, `Person` `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="12010-305">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="12010-306">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="12010-306">Other instances are left unchanged.</span></span>
* <span data-ttu-id="12010-307">Listedeki bir `Person` konuma eklenirse, `<DetailsEditor>` ilgili konuma bir yeni örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="12010-307">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="12010-308">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="12010-308">Other instances are left unchanged.</span></span>
* <span data-ttu-id="12010-309">`Person`Girişler yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="12010-309">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="12010-310">Bazı senaryolarda, kullanımı [`@key`][5] rerendering karmaşıklığını en aza indirir ve odak konumu gıbı Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="12010-310">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="12010-311">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="12010-311">Keys are local to each container element or component.</span></span> <span data-ttu-id="12010-312">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="12010-312">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="12010-313">Anahtar ne zaman kullanılır? \@</span><span class="sxs-lookup"><span data-stu-id="12010-313">When to use \@key</span></span>

<span data-ttu-id="12010-314">Genellikle, [`@key`][5] her bir liste işlendiğinde (örneğin, bir [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) bloğunda) ve tanımlamak için uygun bir değer varsa, bu durum kullanılır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="12010-314">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="12010-315">Bir [`@key`][5] :::no-loc(Blazor)::: nesne değiştiğinde bir öğeyi veya bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="12010-315">You can also use [`@key`][5] to prevent :::no-loc(Blazor)::: from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="12010-316">`@currentPerson`Değişiklik olursa, [`@key`][5] öznitelik yönergesi :::no-loc(Blazor)::: tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="12010-316">If `@currentPerson` changes, the [`@key`][5] attribute directive forces :::no-loc(Blazor)::: to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="12010-317">Değişiklik sırasında hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="12010-317">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="12010-318">Anahtar ne zaman kullanılmaz? \@</span><span class="sxs-lookup"><span data-stu-id="12010-318">When not to use \@key</span></span>

<span data-ttu-id="12010-319">İle yayılma yaparken bir performans maliyeti vardır [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="12010-319">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="12010-320">Performans maliyeti büyük değildir, ancak yalnızca [`@key`][5] öğenin veya bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="12010-320">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="12010-321">[`@key`][5]Kullanılmasa bile, :::no-loc(Blazor)::: alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="12010-321">Even if [`@key`][5] isn't used, :::no-loc(Blazor)::: preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="12010-322">Kullanmanın tek avantajı [`@key`][5] model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="12010-322">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="12010-323">Anahtar için kullanılacak değerler \@</span><span class="sxs-lookup"><span data-stu-id="12010-323">What values to use for \@key</span></span>

<span data-ttu-id="12010-324">Genellikle, için aşağıdaki değer türlerinden birini sağlamak mantıklı olur [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="12010-324">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="12010-325">Model nesne örnekleri (örneğin, `Person` Önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="12010-325">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="12010-326">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="12010-326">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="12010-327">Benzersiz tanımlayıcılar (örneğin, veya türündeki birincil anahtar değerleri `int` `string` `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="12010-327">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="12010-328">Bu değerlerin çakışmayın için kullanıldığından emin olun [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="12010-328">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="12010-329">Aynı üst öğe içinde çakışan değerler algılanırsa, :::no-loc(Blazor)::: eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="12010-329">If clashing values are detected within the same parent element, :::no-loc(Blazor)::: throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="12010-330">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-330">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="12010-331">Üzerine yazılan parametreler</span><span class="sxs-lookup"><span data-stu-id="12010-331">Overwritten parameters</span></span>

<span data-ttu-id="12010-332">:::no-loc(Blazor):::Framework genellikle güvenli üst-alt parametre ataması uygular:</span><span class="sxs-lookup"><span data-stu-id="12010-332">The :::no-loc(Blazor)::: framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="12010-333">Parametrelerin üzerine yazılması beklenmedik.</span><span class="sxs-lookup"><span data-stu-id="12010-333">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="12010-334">Yan etkiler en aza indirilir.</span><span class="sxs-lookup"><span data-stu-id="12010-334">Side-effects are minimized.</span></span> <span data-ttu-id="12010-335">Örneğin, sonsuz işleme döngüleri oluşturabileceğinden ek işlemeye kaçınılmaz.</span><span class="sxs-lookup"><span data-stu-id="12010-335">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="12010-336">Alt bileşen, üst bileşen yeniden oluşturulduğunda varolan değerlerin üzerine yazılacak yeni parametre değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="12010-336">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="12010-337">Bir alt bileşendeki parametre değerlerinin üzerine yazılması, genellikle bileşeni bir veya daha fazla veriye dayalı parametre ile geliştirilirken ve geliştirici alt öğe içindeki bir parametreye doğrudan yazdığında oluşur:</span><span class="sxs-lookup"><span data-stu-id="12010-337">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="12010-338">Alt bileşen, üst bileşenden bir veya daha fazla parametre değeri ile işlenir.</span><span class="sxs-lookup"><span data-stu-id="12010-338">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="12010-339">Alt öğe doğrudan bir parametre değerine yazar.</span><span class="sxs-lookup"><span data-stu-id="12010-339">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="12010-340">Üst bileşen, alt öğenin parametresinin değerini yeniden ekler ve üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="12010-340">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="12010-341">Parametre değerlerinin üzerine yazılması olasılığı, alt bileşenin Özellik ayarlayıcılarına de göre genişletilir.</span><span class="sxs-lookup"><span data-stu-id="12010-341">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="12010-342">**Genel kılavuzumuzdan kendi parametrelerine doğrudan yazılan bileşenler oluşturmamalıdır.**</span><span class="sxs-lookup"><span data-stu-id="12010-342">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="12010-343">Aşağıdaki hatalı bileşeni göz önünde bulundurun `Expander` :</span><span class="sxs-lookup"><span data-stu-id="12010-343">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="12010-344">Alt içeriği işler.</span><span class="sxs-lookup"><span data-stu-id="12010-344">Renders child content.</span></span>
* <span data-ttu-id="12010-345">Bileşen parametresi () ile alt öğe içeriğini gösterir `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="12010-345">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="12010-346">Bileşen doğrudan `Expanded` parametresine yazar, bu, üzerine yazılan parametrelerle ilgili sorunu gösterir ve kaçınılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="12010-346">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="12010-347">`Expander`Bileşen, çağıraetkileyebilecek bir üst bileşene eklenir <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="12010-347">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="12010-348">Başlangıçta, `Expander` bileşenleri özellikleri bir kez değiştiğinde bağımsız olarak davranır `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="12010-348">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="12010-349">Alt bileşenler, durumlarını beklendiği gibi korur.</span><span class="sxs-lookup"><span data-stu-id="12010-349">The child components maintain their states as expected.</span></span> <span data-ttu-id="12010-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Üst öğede çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değeri () olarak döndürülür `true` .</span><span class="sxs-lookup"><span data-stu-id="12010-350">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="12010-351">İkinci `Expander` `Expanded` bileşende hiçbir alt içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.</span><span class="sxs-lookup"><span data-stu-id="12010-351">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="12010-352">Önceki senaryodaki durumu korumak için bileşen içindeki *özel bir alanı* kullanarak, onun geçiş `Expander` durumunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="12010-352">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="12010-353">Aşağıdaki düzeltilen `Expander` bileşen:</span><span class="sxs-lookup"><span data-stu-id="12010-353">The following revised `Expander` component:</span></span>

* <span data-ttu-id="12010-354">Üst öğeden `Expanded` bileşen parametre değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="12010-354">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="12010-355">*private field* `expanded` [OnInitialized olaydaki](xref:blazor/components/lifecycle#component-initialization-methods)bir özel alana () bileşen parametre değerini atar.</span><span class="sxs-lookup"><span data-stu-id="12010-355">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="12010-356">Kendi iç geçiş durumunu korumak için özel alanını kullanır, bu da doğrudan bir parametreye yazmayı nasıl önleyeceğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="12010-356">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="12010-357">Daha fazla bilgi için bkz. [ :::no-loc(Blazor)::: Iki yönlü bağlama hatası (DotNet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="12010-357">For additional information, see [:::no-loc(Blazor)::: Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="12010-358">Öznitelik uygulama</span><span class="sxs-lookup"><span data-stu-id="12010-358">Apply an attribute</span></span>

<span data-ttu-id="12010-359">Öznitelikler :::no-loc(Razor)::: , yönergeyle birlikte bileşenlere uygulanabilir [`@attribute`][7] .</span><span class="sxs-lookup"><span data-stu-id="12010-359">Attributes can be applied to :::no-loc(Razor)::: components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="12010-360">Aşağıdaki örnek, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşen sınıfına özniteliğini uygular:</span><span class="sxs-lookup"><span data-stu-id="12010-360">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="12010-361">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="12010-361">Conditional HTML element attributes</span></span>

<span data-ttu-id="12010-362">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="12010-362">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="12010-363">Değer `false` veya ise `null` , öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="12010-363">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="12010-364">Değer ise `true` , öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="12010-364">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="12010-365">Aşağıdaki örnekte, `IsCompleted` `checked` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini belirler:</span><span class="sxs-lookup"><span data-stu-id="12010-365">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="12010-366">`IsCompleted`İse `true` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="12010-366">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="12010-367">`IsCompleted`İse `false` , onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="12010-367">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="12010-368">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="12010-368">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="12010-369">[`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons).NET türü bir olduğunda, gibi bazı HTML öznitelikleri düzgün şekilde çalışmaz `bool` .</span><span class="sxs-lookup"><span data-stu-id="12010-369">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="12010-370">Bu durumlarda, `string` yerine bir tür kullanın `bool` .</span><span class="sxs-lookup"><span data-stu-id="12010-370">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="12010-371">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="12010-371">Raw HTML</span></span>

<span data-ttu-id="12010-372">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="12010-372">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="12010-373">Ham HTML işlemek için, HTML içeriğini bir değerde sarın `MarkupString` .</span><span class="sxs-lookup"><span data-stu-id="12010-373">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="12010-374">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="12010-374">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="12010-375">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="12010-375">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="12010-376">Aşağıdaki örnek, `MarkupString` bir bileşenin işlenmiş çıktısına STATIK HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="12010-376">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="12010-377">:::no-loc(Razor)::: şablondan</span><span class="sxs-lookup"><span data-stu-id="12010-377">:::no-loc(Razor)::: templates</span></span>

<span data-ttu-id="12010-378">İşleme parçaları, :::no-loc(Razor)::: şablon sözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="12010-378">Render fragments can be defined using :::no-loc(Razor)::: template syntax.</span></span> <span data-ttu-id="12010-379">:::no-loc(Razor)::: Şablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="12010-379">:::no-loc(Razor)::: templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="12010-380">Aşağıdaki örnek, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> bir bileşeni doğrudan bir bileşende nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="12010-380">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="12010-381">Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/components/templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="12010-381">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="12010-382">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="12010-382">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="12010-383">Statik varlıklar</span><span class="sxs-lookup"><span data-stu-id="12010-383">Static assets</span></span>

<span data-ttu-id="12010-384">:::no-loc(Blazor):::Projenin [ `web root (wwwroot)` klasörü](xref:fundamentals/index#web-root)altına statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.</span><span class="sxs-lookup"><span data-stu-id="12010-384">:::no-loc(Blazor)::: follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="12010-385">`/`Statik bir varlık için Web köküne başvurmak üzere temel göreli bir yol () kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-385">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="12010-386">Aşağıdaki örnekte, bu `logo.png` klasörde fiziksel olarak bulunur `{PROJECT ROOT}/wwwroot/images` :</span><span class="sxs-lookup"><span data-stu-id="12010-386">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="12010-387">:::no-loc(Razor):::bileşenler, **not** tilde işareti gösterimini ( `~/` ) desteklemez.</span><span class="sxs-lookup"><span data-stu-id="12010-387">:::no-loc(Razor)::: components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="12010-388">Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="12010-388">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="12010-389">Etiket Yardımcıları bileşenlerde desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="12010-389">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="12010-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro):::no-loc(Razor):::bileşenlerde ( `.razor` Dosyalar) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="12010-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in :::no-loc(Razor)::: components (`.razor` files).</span></span> <span data-ttu-id="12010-391">' De etiket Yardımcısı benzeri işlevsellik sağlamak için :::no-loc(Blazor)::: , etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="12010-391">To provide Tag Helper-like functionality in :::no-loc(Blazor):::, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="12010-392">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="12010-392">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="12010-393">İşleme :::no-loc(Blazor)::: HTML, ölçeklenebilir vektör grafik (SVG) görüntüleri de dahil olmak üzere tarayıcıda desteklenen görüntüler ( `.svg` ) etiketi aracılığıyla desteklenir `<img>` :</span><span class="sxs-lookup"><span data-stu-id="12010-393">Since :::no-loc(Blazor)::: renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="12010-394">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının () CSS kurallarında desteklenir `.css` :</span><span class="sxs-lookup"><span data-stu-id="12010-394">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="12010-395">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="12010-395">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="12010-396">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına ( `.razor` ) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="12010-396">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="12010-397">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve [`@bind`][10] bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="12010-397">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="12010-398">Daha fazla bilgi için bkz. [Içindeki SVG desteği :::no-loc(Blazor)::: (DotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="12010-398">For more information, see [SVG support in :::no-loc(Blazor)::: (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12010-399">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="12010-399">Additional resources</span></span>

* <span data-ttu-id="12010-400"><xref:blazor/security/server/threat-mitigation>: :::no-loc(Blazor Server)::: Kaynak tükenmesi ile Çekişmek zorunda olan uygulamalar oluşturmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="12010-400"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building :::no-loc(Blazor Server)::: apps that must contend with resource exhaustion.</span></span>

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
