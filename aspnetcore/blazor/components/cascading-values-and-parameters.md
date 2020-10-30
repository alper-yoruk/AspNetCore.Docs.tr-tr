---
title: BlazorBasamaklı değerleri ve parametreleri ASP.NET Core
author: guardrex
description: Bir üst bileşenden verileri alt bileşenlere nasıl akacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: dec71f881dc815a26d62da6df92118926b1dcf06
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056432"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>BlazorBasamaklı değerleri ve parametreleri ASP.NET Core

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](xref:blazor/components/index#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir. Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir. Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.

### <a name="theme-example"></a>Tema örneği

Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir. <xref:Microsoft.AspNetCore.Components.CascadingValue%601>Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.

Örneğin, örnek uygulama, `ThemeInfo` uygulamanın düzenleriyle, özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir `@Body` . `ButtonClass` öğesinin bir değeri, `btn-success` Düzen bileşeninde atanır. Tüm alt bileşenler bu özelliği basamaklı nesne aracılığıyla kullanabilir `ThemeInfo` .

`CascadingValuesParametersLayout` bileşeninde

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

Basamaklı değerlerin kullanılması için, bileşenler özniteliği kullanarak Geçişli Parametreler bildirir [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) . Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.

Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar. Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.

`CascadingValuesParametersTheme` bileşeninde

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

Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> her <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşene ve karşılık gelen özniteliğine benzersiz bir dize sağlayın [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) . Aşağıdaki örnekte, iki <xref:Microsoft.AspNetCore.Components.CascadingValue%601> bileşen adına göre farklı örneklerini basamakla `MyCascadingType` :

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

Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet örneği

Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır. Örneğin, örnek uygulamada aşağıdaki örneği göz önünde bulundurun `TabSet` .

Örnek uygulamada, `ITab` sekmelerin uygulandığı bir arabirim vardır:

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet`Bileşen, `TabSet` çeşitli bileşenleri içeren bileşenini kullanır `Tab` :

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

Alt `Tab` Bileşenler, öğesine açıkça parametre olarak aktarılmaz `TabSet` . Bunun yerine, alt `Tab` bileşenleri öğesinin alt içeriğinin bir parçasıdır `TabSet` . Bununla birlikte, `TabSet` `Tab` üst bilgileri ve etkin sekmeyi işleyebilmesi için her bileşen hakkında hala bilmeniz gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için bileşen, `TabSet` kendisini alt bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* `Tab` .

`TabSet` bileşeninde

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Alt `Tab` Bileşenler, kapsayan ' `TabSet` i basamaklı bir parametre olarak yakalar, bu nedenle bileşenler, `Tab` `TabSet` Bu sekmenin etkin olduğu ve koordinasyonu üzerine eklenir.

`Tab` bileşeninde

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
