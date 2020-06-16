---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: Sayfalardaki ve görünümlerde bileşenleri işlemek için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin Razor .
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: b6e3890e13ef5ad20098d3907b6895046087aeca
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776507"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="37178-103">ASP.NET Core bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="37178-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="37178-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="37178-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="37178-105">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="37178-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="37178-106">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="37178-106">Prerequisites</span></span>

<span data-ttu-id="37178-107">Makalenin *Sayfalar ve görünümlerde bileşenleri kullanmak için uygulamayı hazırlama* bölümündeki yönergeleri izleyin <xref:blazor/integrate-components#prepare-the-app> .</span><span class="sxs-lookup"><span data-stu-id="37178-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="37178-108">Bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="37178-108">Component Tag Helper</span></span>

<span data-ttu-id="37178-109">Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir sayfada veya görünümde işler:</span><span class="sxs-lookup"><span data-stu-id="37178-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="37178-110">Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="37178-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="37178-111">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="37178-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="37178-112">Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="37178-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="37178-113">`ColorfulCheckbox`Onay kutusu etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="37178-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="37178-114">`Size`( `int` ) Ve `Color` ( `string` ) [bileşen parametreleri](xref:blazor/components#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="37178-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="37178-115">Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="37178-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="37178-116">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="37178-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="37178-117">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="37178-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="37178-118">Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği gibi [açık bir Razor ifade](xref:mvc/views/razor#explicit-razor-expressions)gerektirir `param-Color` .</span><span class="sxs-lookup"><span data-stu-id="37178-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="37178-119">Razor `string` `param-*` Öznitelik bir tür olduğundan, bir tür değeri için ayrıştırma davranışı bir özniteliğe uygulanmaz `object` .</span><span class="sxs-lookup"><span data-stu-id="37178-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="37178-120">Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="37178-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="37178-121">Örneğin, `Size` `Color` ve TÜRLERI, `Size` `Color` `int` `string` JSON seri hale getiricisi tarafından desteklenen temel türler (ve) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="37178-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="37178-122">Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:</span><span class="sxs-lookup"><span data-stu-id="37178-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="37178-123">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="37178-123">*MyClass.cs*:</span></span>

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

<span data-ttu-id="37178-124">**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="37178-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="37178-125">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37178-125">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="37178-126">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="37178-126">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="37178-127">Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="37178-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="37178-128">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample` ve `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="37178-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="37178-129">`MyClass`, uygulamanın ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="37178-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="37178-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="37178-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="37178-131">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="37178-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="37178-132">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="37178-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="37178-133">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="37178-133">Render Mode</span></span> | <span data-ttu-id="37178-134">Açıklama</span><span class="sxs-lookup"><span data-stu-id="37178-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="37178-135">Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="37178-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="37178-136">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="37178-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="37178-137">Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="37178-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="37178-138">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="37178-138">Output from the component isn't included.</span></span> <span data-ttu-id="37178-139">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="37178-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="37178-140">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="37178-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="37178-141">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="37178-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="37178-142">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="37178-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="37178-143">Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="37178-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="37178-144">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="37178-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37178-145">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="37178-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
