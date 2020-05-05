---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: Sayfalardaki ve görünümlerde bileşenleri işlemek Razor Için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773935"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="6fea6-103">ASP.NET Core bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="6fea6-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="6fea6-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="6fea6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6fea6-105">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="6fea6-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6fea6-106">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6fea6-106">Prerequisites</span></span>

<span data-ttu-id="6fea6-107"><xref:blazor/integrate-components#prepare-the-app> Makalenin *Sayfalar ve görünümlerde bileşenleri kullanmak için uygulamayı hazırlama* bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="6fea6-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="6fea6-108">Bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="6fea6-108">Component Tag Helper</span></span>

<span data-ttu-id="6fea6-109">Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir sayfada veya görünümde işler:</span><span class="sxs-lookup"><span data-stu-id="6fea6-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="6fea6-110">Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="6fea6-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="6fea6-111">Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="6fea6-112">Onay kutusu etiketinin `ColorfulCheckbox` rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="6fea6-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="6fea6-113">`Size` (`int`) Ve `Color` (`string`) [bileşen parametreleri](xref:blazor/components#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="6fea6-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="6fea6-114">Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="6fea6-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="6fea6-115">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="6fea6-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="6fea6-116">Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği `param-Color` gibi açık bir [Razor ifadesi](xref:mvc/views/razor#explicit-razor-expressions)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="6fea6-117">Öznitelik bir `object` tür olduğundan, bir `string` tür değeri için Razor ayrıştırma davranışı `param-*` bir özniteliğe uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="6fea6-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="6fea6-118">Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="6fea6-119">`Size` Örneğin, `Color` `Size` `Color` ve türleri, JSON seri hale getiricisi tarafından desteklenen temel türler (`int` ve `string`) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6fea6-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="6fea6-120">Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:</span><span class="sxs-lookup"><span data-stu-id="6fea6-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="6fea6-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="6fea6-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="6fea6-122">**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="6fea6-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="6fea6-123">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6fea6-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="6fea6-124">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6fea6-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="6fea6-125">Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="6fea6-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="6fea6-126">`MyClass`uygulamanın ad alanında (`{APP ASSEMBLY}`) bulunur.</span><span class="sxs-lookup"><span data-stu-id="6fea6-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="6fea6-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="6fea6-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="6fea6-128">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="6fea6-129">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="6fea6-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="6fea6-130">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="6fea6-130">Render Mode</span></span> | <span data-ttu-id="6fea6-131">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6fea6-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="6fea6-132">Bileşeni statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6fea6-133">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6fea6-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="6fea6-134">Blazor Sunucu uygulaması için bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6fea6-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6fea6-135">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-135">Output from the component isn't included.</span></span> <span data-ttu-id="6fea6-136">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6fea6-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="6fea6-137">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="6fea6-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6fea6-138">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="6fea6-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="6fea6-139">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="6fea6-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="6fea6-140">Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="6fea6-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="6fea6-141">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="6fea6-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6fea6-142">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6fea6-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
