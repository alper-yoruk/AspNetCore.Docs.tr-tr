---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: "Sayfalardaki ve görünümlerde bileşenleri işlemek için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin :::no-loc(Razor)::: ."
ms.custom: mvc
ms.date: 10/29/2020
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
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431049"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="34d11-103">ASP.NET Core bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="34d11-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="34d11-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="34d11-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34d11-105">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="34d11-105">Prerequisites</span></span>

<span data-ttu-id="34d11-106">*Yapılandırma* bölümündeki yönergeleri izleyerek şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="34d11-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="34d11-107">Bileşen etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="34d11-107">Component Tag Helper</span></span>

<span data-ttu-id="34d11-108">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag) kullanın.</span><span class="sxs-lookup"><span data-stu-id="34d11-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="34d11-109">:::no-loc(Razor)::: :::no-loc(Razor)::: *Barındırılan bir :::no-loc(Blazor WebAssembly)::: UYGULAMADAKI* bileşenleri sayfalar ve MVC uygulamalarıyla tümleştirmek, .net 5,0 veya sonraki sürümlerde ASP.NET Core desteklenir.</span><span class="sxs-lookup"><span data-stu-id="34d11-109">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="34d11-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="34d11-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="34d11-111">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="34d11-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="34d11-112">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="34d11-113">:::no-loc(Blazor WebAssembly)::: uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="34d11-113">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="34d11-114">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="34d11-114">Render Mode</span></span> | <span data-ttu-id="34d11-115">Description</span><span class="sxs-lookup"><span data-stu-id="34d11-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="34d11-116">Bir :::no-loc(Blazor WebAssembly)::: uygulamanın tarayıcıya yüklendiğinde etkileşimli bir bileşeni içermesi için kullanılacak bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="34d11-116">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="34d11-117">Bileşen ön işlenmiş değildir.</span><span class="sxs-lookup"><span data-stu-id="34d11-117">The component isn't prerendered.</span></span> <span data-ttu-id="34d11-118">Bu seçenek farklı sayfalarda farklı bileşenlerin işlenmesine daha kolay hale gelir :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-118">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="34d11-119">Bileşeni statik HTML 'ye ön ekler ve :::no-loc(Blazor WebAssembly)::: daha sonra, tarayıcıya yüklendiğinde bileşeni etkileşimli hale getirmek için bir uygulamanın işaretçisini içerir.</span><span class="sxs-lookup"><span data-stu-id="34d11-119">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="34d11-120">:::no-loc(Blazor Server)::: uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="34d11-120">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="34d11-121">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="34d11-121">Render Mode</span></span> | <span data-ttu-id="34d11-122">Description</span><span class="sxs-lookup"><span data-stu-id="34d11-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="34d11-123">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-123">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="34d11-124">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-124">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="34d11-125">Bir uygulama için işaretleyici işler :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-125">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="34d11-126">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="34d11-126">Output from the component isn't included.</span></span> <span data-ttu-id="34d11-127">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-127">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="34d11-128">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="34d11-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="34d11-129">:::no-loc(Blazor Server)::: uygulama işleme modları aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="34d11-129">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="34d11-130">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="34d11-130">Render Mode</span></span> | <span data-ttu-id="34d11-131">Description</span><span class="sxs-lookup"><span data-stu-id="34d11-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="34d11-132">Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-132">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="34d11-133">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-133">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="34d11-134">Bir uygulama için işaretleyici işler :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-134">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="34d11-135">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="34d11-135">Output from the component isn't included.</span></span> <span data-ttu-id="34d11-136">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="34d11-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="34d11-137">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="34d11-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="34d11-138">Ek özellikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="34d11-138">Additional characteristics include:</span></span>

* <span data-ttu-id="34d11-139">Birden çok bileşeni işlemeye yönelik birden çok bileşen etiketi yardımcıya :::no-loc(Razor)::: izin veriliyor.</span><span class="sxs-lookup"><span data-stu-id="34d11-139">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="34d11-140">Uygulama başladıktan sonra bileşenler dinamik olarak işlenemiyor.</span><span class="sxs-lookup"><span data-stu-id="34d11-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="34d11-141">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="34d11-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="34d11-142">Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="34d11-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="34d11-143">Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="34d11-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="34d11-144">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="34d11-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="34d11-145">Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir uygulamada bir sayfada veya görünümünde işler :::no-loc(Blazor Server)::: `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="34d11-145">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="34d11-146">Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="34d11-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="34d11-147">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample.Pages` veya `@using :::no-loc(Blazor):::Sample.Client.Pages` barındırılan bir :::no-loc(Blazor)::: çözümde).</span><span class="sxs-lookup"><span data-stu-id="34d11-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="34d11-148">Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="34d11-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="34d11-149">`ColorfulCheckbox`Onay kutusu etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="34d11-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="34d11-150">`Size`( `int` ) Ve `Color` ( `string` ) [bileşen parametreleri](xref:blazor/components/index#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="34d11-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="34d11-151">Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="34d11-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="34d11-152">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="34d11-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="34d11-153">Aşağıdaki HTML sayfada veya görünümde işlenir:</span><span class="sxs-lookup"><span data-stu-id="34d11-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="34d11-154">Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği gibi [açık bir :::no-loc(Razor)::: ifade](xref:mvc/views/razor#explicit-razor-expressions)gerektirir `param-Color` .</span><span class="sxs-lookup"><span data-stu-id="34d11-154">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="34d11-155">:::no-loc(Razor)::: `string` `param-*` Öznitelik bir tür olduğundan, bir tür değeri için ayrıştırma davranışı bir özniteliğe uygulanmaz `object` .</span><span class="sxs-lookup"><span data-stu-id="34d11-155">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="34d11-156">Aşağıdakiler dışında tüm parametre türleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="34d11-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="34d11-157">Genel parametreler.</span><span class="sxs-lookup"><span data-stu-id="34d11-157">Generic parameters.</span></span>
* <span data-ttu-id="34d11-158">Seri hale getirilebilir olmayan parametreler.</span><span class="sxs-lookup"><span data-stu-id="34d11-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="34d11-159">Koleksiyon parametrelerinde devralma.</span><span class="sxs-lookup"><span data-stu-id="34d11-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="34d11-160">Türü :::no-loc(Blazor WebAssembly)::: uygulamanın dışında veya geç tarafından yüklenen bir derlemede tanımlanan parametreler.</span><span class="sxs-lookup"><span data-stu-id="34d11-160">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="34d11-161">Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="34d11-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="34d11-162">Örneğin, `Size` `Color` ve TÜRLERI, `Size` `Color` `int` `string` JSON seri hale getiricisi tarafından desteklenen temel türler (ve) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34d11-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="34d11-163">Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:</span><span class="sxs-lookup"><span data-stu-id="34d11-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="34d11-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="34d11-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="34d11-165">**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**</span><span class="sxs-lookup"><span data-stu-id="34d11-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="34d11-166">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="34d11-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="34d11-167">*Pages/MyPage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="34d11-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="34d11-168">Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="34d11-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="34d11-169">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using :::no-loc(Blazor):::Sample` ve `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="34d11-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="34d11-170">`MyClass` , uygulamanın ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="34d11-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34d11-171">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="34d11-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
