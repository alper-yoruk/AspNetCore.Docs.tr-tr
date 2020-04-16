---
title: ASP.NET Core'da Bileşen Etiket Yardımcısı
author: guardrex
ms.author: riande
description: Razor bileşenlerini sayfalarda ve görünümlerde işlemek için ASP.NET Temel Bileşen Tag Helper'ı nasıl kullanacağınızı öğrenin.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440967"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="15158-103">ASP.NET Core'da Bileşen Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="15158-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="15158-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="15158-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="15158-105">Bir bileşeni bir sayfa veya görünümden işlemek için [Bileşen Etiket Yardımcısı'nı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="15158-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="15158-106">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="15158-106">Prerequisites</span></span>

<span data-ttu-id="15158-107"><xref:blazor/integrate-components#prepare-the-app> Makalenin sayfalar *ve görünümler bölümündeki bileşenleri kullanmak için uygulamayı Hazırla'daki* kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="15158-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="15158-108">Bileşen Etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="15158-108">Component Tag Helper</span></span>

<span data-ttu-id="15158-109">Aşağıdaki Bileşen Tag Yardımcısı `Counter` bileşeni bir sayfada veya görünümde işler:</span><span class="sxs-lookup"><span data-stu-id="15158-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="15158-110">Önceki örnek, bileşenin `Counter` uygulamanın *Sayfalar* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="15158-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="15158-111">Bileşen Tag Yardımcısı da bileşenleri parametreleri geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="15158-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="15158-112">Onay kutusu `ColorfulCheckbox` etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="15158-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="15158-113">`Size` (`int`) `Color` ve`string`( ) [bileşen parametreleri](xref:blazor/components#component-parameters) Bileşen Tag Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="15158-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="15158-114">Önceki örnek, bileşenin `ColorfulCheckbox` uygulamanın *Paylaşılan* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="15158-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="15158-115">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="15158-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="15158-116">Teklif edilen bir dize yi geçmek, `param-Color` önceki örnekte gösterildiği gibi açık bir [Razor ifadesi](xref:mvc/views/razor#explicit-razor-expressions)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="15158-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="15158-117">Tür değeri için `string` Razor ayrıştırma davranışı öznitelik `param-*` bir `object` tür olduğundan öznitelik için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="15158-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="15158-118">Parametre türü JSON serileştirilebilir olmalıdır, bu da genellikle türün varsayılan bir oluşturucu ve ayarlanabilir özelliklere sahip olması gerektiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="15158-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="15158-119">Örneğin, json serileştiricisi `Color` tarafından desteklenen ilkel türleri `Size` (ve) `Color` `int` `string`türleri olduğundan, önceki örnekte bir değer `Size` belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="15158-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="15158-120">Aşağıdaki örnekte, bir sınıf nesnesi bileşene aktarılır:</span><span class="sxs-lookup"><span data-stu-id="15158-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="15158-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="15158-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="15158-122">**Sınıfın ortak parametresiz bir oluşturucusu olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="15158-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="15158-123">*Paylaşılan/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="15158-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="15158-124">*Sayfalar/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15158-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="15158-125">Önceki örnek, bileşenin `MyComponent` uygulamanın *Paylaşılan* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="15158-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="15158-126">`MyClass`uygulamanın ad alanındadır (`{APP ASSEMBLY}`).</span><span class="sxs-lookup"><span data-stu-id="15158-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="15158-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin:</span><span class="sxs-lookup"><span data-stu-id="15158-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="15158-128">Sayfaya önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="15158-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="15158-129">Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="15158-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="15158-130">Render Modu</span><span class="sxs-lookup"><span data-stu-id="15158-130">Render Mode</span></span> | <span data-ttu-id="15158-131">Açıklama</span><span class="sxs-lookup"><span data-stu-id="15158-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="15158-132">Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir.</span><span class="sxs-lookup"><span data-stu-id="15158-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="15158-133">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="15158-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="15158-134">Blazor Sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="15158-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="15158-135">Bileşenden gelen çıktı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="15158-135">Output from the component isn't included.</span></span> <span data-ttu-id="15158-136">Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="15158-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="15158-137">Bileşeni statik HTML'ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="15158-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="15158-138">Sayfalar ve görünümler bileşenleri kullanabilse de, tam tersi doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="15158-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="15158-139">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="15158-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="15158-140">Bileşendeki kısmi görünümden mantığı kullanmak için, kısmi görünüm mantığını bileşene dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="15158-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="15158-141">Sunucu bileşenlerinin statik bir HTML sayfasından görüntülenmeleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="15158-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15158-142">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="15158-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
