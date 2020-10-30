---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: "Sayfalardaki ve görünümlerde bileşenleri işlemek için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin :::no-loc(Razor)::: ."
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: cddbca7f95e4d2143d4632aaa83133bc6210e251
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059162"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="c1767-103">ASP.NET Core bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="c1767-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="c1767-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="c1767-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1767-105">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="c1767-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c1767-106">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="c1767-106">Prerequisites</span></span>

<span data-ttu-id="c1767-107">Makalenin *Sayfalar ve görünümlerde bileşenleri kullanmak için uygulamayı hazırlama* bölümündeki yönergeleri izleyin <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> .</span><span class="sxs-lookup"><span data-stu-id="c1767-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="c1767-108">Bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="c1767-108">Component Tag Helper</span></span>

<span data-ttu-id="c1767-109">Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir sayfada veya görünümde işler:</span><span class="sxs-lookup"><span data-stu-id="c1767-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="c1767-110">Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c1767-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="c1767-111">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="c1767-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages`).</span></span>

<span data-ttu-id="c1767-112">Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="c1767-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="c1767-113">`ColorfulCheckbox`Onay kutusu etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c1767-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="c1767-114">`Size`( `int` ) Ve `Color` ( `string` ) [bileşen parametreleri](xref:blazor/components/index#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c1767-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="c1767-115">Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c1767-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="c1767-116">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="c1767-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="c1767-117">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="c1767-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="c1767-118">Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği gibi [açık bir :::no-loc(Razor)::: ifade](xref:mvc/views/razor#explicit-razor-expressions)gerektirir `param-Color` .</span><span class="sxs-lookup"><span data-stu-id="c1767-118">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="c1767-119">:::no-loc(Razor)::: `string` `param-*` Öznitelik bir tür olduğundan, bir tür değeri için ayrıştırma davranışı bir özniteliğe uygulanmaz `object` .</span><span class="sxs-lookup"><span data-stu-id="c1767-119">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="c1767-120">Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c1767-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="c1767-121">Örneğin, `Size` `Color` ve TÜRLERI, `Size` `Color` `int` `string` JSON seri hale getiricisi tarafından desteklenen temel türler (ve) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c1767-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="c1767-122">Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:</span><span class="sxs-lookup"><span data-stu-id="c1767-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="c1767-123">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="c1767-123">*MyClass.cs* :</span></span>

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

<span data-ttu-id="c1767-124">**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="c1767-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="c1767-125">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c1767-125">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="c1767-126">*Pages/MyPage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c1767-126">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="c1767-127">Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="c1767-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="c1767-128">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample` ve `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="c1767-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="c1767-129">`MyClass` , uygulamanın ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="c1767-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="c1767-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="c1767-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="c1767-131">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="c1767-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="c1767-132">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="c1767-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

| <span data-ttu-id="c1767-133">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="c1767-133">Render Mode</span></span> | <span data-ttu-id="c1767-134">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c1767-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c1767-135">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="c1767-135">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="c1767-136">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="c1767-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c1767-137">Bir uygulama için işaretleyici işler :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="c1767-137">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="c1767-138">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c1767-138">Output from the component isn't included.</span></span> <span data-ttu-id="c1767-139">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="c1767-139">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c1767-140">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="c1767-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c1767-141">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="c1767-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c1767-142">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="c1767-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="c1767-143">Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="c1767-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c1767-144">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="c1767-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1767-145">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c1767-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
