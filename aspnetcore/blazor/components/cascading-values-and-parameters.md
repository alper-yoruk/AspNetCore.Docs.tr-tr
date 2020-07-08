---
title: BlazorBasamaklı değerleri ve parametreleri ASP.NET Core
author: guardrex
description: Bir üst bileşenden verileri alt bileşenlere nasıl akacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c426be21b520520c6745ada95be35816f7365c21
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059935"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="77d99-103">BlazorBasamaklı değerleri ve parametreleri ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77d99-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="77d99-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="77d99-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="77d99-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77d99-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="77d99-106">Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](xref:blazor/components/index#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="77d99-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="77d99-107">Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir.</span><span class="sxs-lookup"><span data-stu-id="77d99-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="77d99-108">Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="77d99-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="77d99-109">Tema örneği</span><span class="sxs-lookup"><span data-stu-id="77d99-109">Theme example</span></span>

<span data-ttu-id="77d99-110">Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="77d99-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="77d99-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="77d99-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="77d99-112">Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77d99-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="77d99-113"><xref:Microsoft.AspNetCore.Components.CascadingValue%601>Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="77d99-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="77d99-114">Örneğin, örnek uygulama, `ThemeInfo` uygulamanın düzenleriyle, özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir `@Body` .</span><span class="sxs-lookup"><span data-stu-id="77d99-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="77d99-115">`ButtonClass`öğesinin bir değeri, `btn-success` Düzen bileşeninde atanır.</span><span class="sxs-lookup"><span data-stu-id="77d99-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="77d99-116">Tüm alt bileşenler bu özelliği basamaklı nesne aracılığıyla kullanabilir `ThemeInfo` .</span><span class="sxs-lookup"><span data-stu-id="77d99-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="77d99-117">`CascadingValuesParametersLayout`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="77d99-117">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="77d99-118">Basamaklı değerlerin kullanılması için, bileşenler özniteliği kullanarak Geçişli Parametreler bildirir [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="77d99-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="77d99-119">Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="77d99-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="77d99-120">Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="77d99-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="77d99-121">Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d99-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="77d99-122">`CascadingValuesParametersTheme`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="77d99-122">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="77d99-123">Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> her <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşene ve karşılık gelen özniteliğine benzersiz bir dize sağlayın [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="77d99-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="77d99-124">Aşağıdaki örnekte, iki <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşen adına göre farklı örneklerini basamakla `MyCascadingType` :</span><span class="sxs-lookup"><span data-stu-id="77d99-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
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

<span data-ttu-id="77d99-125">Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:</span><span class="sxs-lookup"><span data-stu-id="77d99-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="77d99-126">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="77d99-126">TabSet example</span></span>

<span data-ttu-id="77d99-127">Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="77d99-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="77d99-128">Örneğin, örnek uygulamada aşağıdaki örneği göz önünde bulundurun `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="77d99-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="77d99-129">Örnek uygulamada, `ITab` sekmelerin uygulandığı bir arabirim vardır:</span><span class="sxs-lookup"><span data-stu-id="77d99-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="77d99-130">`CascadingValuesParametersTabSet`Bileşen, `TabSet` çeşitli bileşenleri içeren bileşenini kullanır `Tab` :</span><span class="sxs-lookup"><span data-stu-id="77d99-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

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

@code {
    private bool showThirdTab;
}
```

<span data-ttu-id="77d99-131">Alt `Tab` Bileşenler, öğesine açıkça parametre olarak aktarılmaz `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="77d99-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="77d99-132">Bunun yerine, alt `Tab` bileşenleri öğesinin alt içeriğinin bir parçasıdır `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="77d99-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="77d99-133">Bununla birlikte, `TabSet` `Tab` üst bilgileri ve etkin sekmeyi işleyebilmesi için her bileşen hakkında hala bilmeniz gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için bileşen, `TabSet` kendisini alt bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* `Tab` .</span><span class="sxs-lookup"><span data-stu-id="77d99-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="77d99-134">`TabSet`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="77d99-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="77d99-135">Alt `Tab` Bileşenler, kapsayan ' `TabSet` i basamaklı bir parametre olarak yakalar, bu nedenle bileşenler, `Tab` `TabSet` Bu sekmenin etkin olduğu ve koordinasyonu üzerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="77d99-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="77d99-136">`Tab`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="77d99-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
