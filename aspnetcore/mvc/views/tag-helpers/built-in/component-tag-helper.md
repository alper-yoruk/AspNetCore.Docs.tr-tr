---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: "Sayfalardaki ve görünümlerde bileşenleri işlemek için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin Razor ."
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595460"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="c024a-103">ASP.NET Core bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="c024a-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="c024a-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="c024a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c024a-105">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="c024a-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c024a-106">*Yapılandırma* bölümündeki yönergeleri izleyerek şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="c024a-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c024a-107">Makalenin *yapılandırma* bölümündeki yönergeleri izleyin <xref:blazor/components/prerendering-and-integration?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="c024a-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="c024a-108">Bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="c024a-108">Component Tag Helper</span></span>

<span data-ttu-id="c024a-109">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag) kullanın.</span><span class="sxs-lookup"><span data-stu-id="c024a-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="c024a-110">Razor Razor *Barındırılan bir Blazor WebAssembly UYGULAMADAKI* bileşenleri sayfalar ve MVC uygulamalarıyla tümleştirmek, .net 5,0 veya sonraki sürümlerde ASP.NET Core desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c024a-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="c024a-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="c024a-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="c024a-112">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="c024a-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="c024a-113">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="c024a-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c024a-114">Blazor WebAssembly uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c024a-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="c024a-115">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="c024a-115">Render Mode</span></span> | <span data-ttu-id="c024a-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c024a-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="c024a-117">Bir Blazor WebAssembly uygulamanın tarayıcıya yüklendiğinde etkileşimli bir bileşeni içermesi için kullanılacak bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c024a-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="c024a-118">Bileşen ön işlenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="c024a-118">The component isn't prerendered.</span></span> <span data-ttu-id="c024a-119">Bu seçenek farklı sayfalarda farklı bileşenlerin işlenmesine daha kolay hale gelir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="c024a-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="c024a-120">Bileşeni statik HTML 'ye ön ekler ve Blazor WebAssembly daha sonra, tarayıcıya yüklendiğinde bileşeni etkileşimli hale getirmek için bir uygulamanın işaretçisini içerir.</span><span class="sxs-lookup"><span data-stu-id="c024a-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="c024a-121">Blazor Server uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c024a-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="c024a-122">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="c024a-122">Render Mode</span></span> | <span data-ttu-id="c024a-123">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c024a-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c024a-124">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="c024a-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c024a-125">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="c024a-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c024a-126">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="c024a-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c024a-127">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c024a-127">Output from the component isn't included.</span></span> <span data-ttu-id="c024a-128">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="c024a-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c024a-129">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="c024a-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c024a-130">Blazor Server uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c024a-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="c024a-131">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="c024a-131">Render Mode</span></span> | <span data-ttu-id="c024a-132">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c024a-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c024a-133">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="c024a-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c024a-134">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="c024a-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c024a-135">Bir uygulama için işaretleyici işler Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="c024a-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c024a-136">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c024a-136">Output from the component isn't included.</span></span> <span data-ttu-id="c024a-137">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="c024a-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c024a-138">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="c024a-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="c024a-139">Ek özellikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c024a-139">Additional characteristics include:</span></span>

* <span data-ttu-id="c024a-140">Birden çok bileşeni işlemeye yönelik birden çok bileşen etiketi yardımcıya Razor izin veriliyor.</span><span class="sxs-lookup"><span data-stu-id="c024a-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="c024a-141">Uygulama başladıktan sonra bileşenler dinamik olarak işlenemiyor.</span><span class="sxs-lookup"><span data-stu-id="c024a-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="c024a-142">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="c024a-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c024a-143">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="c024a-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="c024a-144">Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="c024a-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="c024a-145">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="c024a-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="c024a-146">Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir uygulamada bir sayfada veya görünümünde işler Blazor Server `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="c024a-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="c024a-147">Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c024a-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="c024a-148">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Pages` veya `@using BlazorSample.Client.Pages` barındırılan bir Blazor çözümde).</span><span class="sxs-lookup"><span data-stu-id="c024a-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="c024a-149">Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="c024a-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="c024a-150">`ColorfulCheckbox`Onay kutusu etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c024a-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="c024a-151">`Size`( `int` ) Ve `Color` ( `string` ) [bileşen parametreleri](xref:blazor/components/index#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c024a-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="c024a-152">Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c024a-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="c024a-153">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="c024a-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="c024a-154">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="c024a-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="c024a-155">Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği gibi [açık bir Razor ifade](xref:mvc/views/razor#explicit-razor-expressions)gerektirir `param-Color` .</span><span class="sxs-lookup"><span data-stu-id="c024a-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="c024a-156">Razor `string` `param-*` Öznitelik bir tür olduğundan, bir tür değeri için ayrıştırma davranışı bir özniteliğe uygulanmaz `object` .</span><span class="sxs-lookup"><span data-stu-id="c024a-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="c024a-157">Aşağıdakiler dışında tüm parametre türleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="c024a-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="c024a-158">Genel parametreler.</span><span class="sxs-lookup"><span data-stu-id="c024a-158">Generic parameters.</span></span>
* <span data-ttu-id="c024a-159">Seri hale getirilebilir olmayan parametreler.</span><span class="sxs-lookup"><span data-stu-id="c024a-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="c024a-160">Koleksiyon parametrelerinde devralma.</span><span class="sxs-lookup"><span data-stu-id="c024a-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="c024a-161">Türü Blazor WebAssembly uygulamanın dışında veya geç tarafından yüklenen bir derlemede tanımlanan parametreler.</span><span class="sxs-lookup"><span data-stu-id="c024a-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="c024a-162">Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c024a-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="c024a-163">Örneğin, `Size` `Color` ve TÜRLERI, `Size` `Color` `int` `string` JSON seri hale getiricisi tarafından desteklenen temel türler (ve) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c024a-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="c024a-164">Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:</span><span class="sxs-lookup"><span data-stu-id="c024a-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="c024a-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="c024a-165">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="c024a-166">**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="c024a-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="c024a-167">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c024a-167">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="c024a-168">*Pages/MyPage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c024a-168">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="c024a-169">Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c024a-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="c024a-170">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample` ve `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="c024a-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="c024a-171">`MyClass` , uygulamanın ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="c024a-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c024a-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c024a-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
