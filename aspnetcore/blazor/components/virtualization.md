---
title: ASP.NET Core Blazor bileşen Sanallaştırması
author: guardrex
description: ASP.NET Core uygulamalarda bileşen sanallaştırmayı nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
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
uid: blazor/components/virtualization
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653886"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor bileşen Sanallaştırması

[Daniel Roth](https://github.com/danroth27) tarafından

Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir Blazor . Sanallaştırma, UI işlemesini yalnızca şu anda görünür olan bölümlerle sınırlamak için bir tekniktir. Örneğin sanallaştırma, uygulamanın uzun bir öğe listesini işlemesi gerektiğinde ve belirli bir zamanda bir öğe alt kümesinin görünür olması gerektiğinde faydalıdır. Blazor`Virtualize`uygulamanın bileşenlerine sanallaştırma eklemek için kullanılabilecek bileşeni sağlar.

::: moniker range=">= aspnetcore-5.0"

Sanallaştırma olmadan tipik bir liste, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) listedeki her öğeyi işlemek için bir C# döngüsü kullanabilir:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Listede binlerce öğe varsa, listeyi işlemek uzun zaman alabilir. Kullanıcı, fark edilebilir bir UI gecikmesi yaşayabilir.

Listedeki her öğeyi tek seferde işlemek yerine, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngüsünü `Virtualize` bileşeniyle değiştirin ve ile birlikte bir sabit öğe kaynağı belirtin `Items` . Yalnızca şu anda görünür olan öğeler işlenir:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Bileşen için bir bağlam belirtmiyorsanız `Context` , `context` `@context.{PROPERTY}` öğe içerik şablonunda () değerini kullanın:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize`Bileşen kapsayıcının yüksekliğine ve işlenmiş öğelerin boyutuna göre işlenecek öğe sayısını hesaplar.

Bileşen için öğe içeriği `Virtualize` şunları içerebilir:

* Yukarıdaki örnekte gösterildiği gibi düz HTML ve Razor kod.
* Bir veya daha fazla Razor bileşen.
* HTML/ Razor ve Razor bileşenleri karışımı.

## <a name="item-provider-delegate"></a>Öğe sağlayıcısı temsilcisi

Tüm öğeleri belleğe yüklemek istemiyorsanız, `ItemsProvider` istenen öğeleri istek üzerine zaman uyumsuz olarak alan bileşenin parametresine bir öğe sağlayıcısı temsilci yöntemi belirtebilirsiniz:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Öğe sağlayıcısı `ItemsProviderRequest` , belirli bir başlangıç dizininden başlayarak gereken sayıda öğeyi belirten bir alır. Daha sonra öğe sağlayıcısı, istenen öğeleri bir veritabanından veya başka bir hizmetten alır ve bunları `ItemsProviderResult<TItem>` Toplam öğe sayısı ile birlikte döndürür. Öğe sağlayıcısı her bir istek ile öğeleri almayı seçebilir veya bunları hazır olmaları için önbelleğe alabilir. Aynı bileşen için bir öğe sağlayıcısı kullanmayı ve koleksiyonuna bir koleksiyon atamayı denemeyin `Items` `Virtualize` .

Aşağıdaki örnek, çalışanları bir ile yükler `EmployeeService` :

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Yer tutucu

Uzak bir veri kaynağından gelen öğelerin bir süre sürebileceğinden, `<Placeholder>...</Placeholder>` öğe verileri kullanılabilir olana kadar bir yer tutucu () işleme seçeneğiniz vardır:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Öğe boyutu

Her öğenin piksel cinsinden boyutu ayarlanabilir `ItemSize` (varsayılan: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Fazla tarama sayısı

`OverscanCount` görünen bölgeden önce ve sonra kaç tane ek öğenin işleneceğini belirler. Bu ayar, kaydırma sırasında işleme sıklığını azaltmaya yardımcı olur. Ancak, daha yüksek değerler sayfada işlenen daha fazla öğenin oluşmasına neden olur (varsayılan: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Örneğin, bileşenleri içeren yüzlerce satırı işleyen bir kılavuz veya liste, işlemcinin işlenmesi için yoğun bir işlemdir. Belirli bir zamanda bileşenlerin yalnızca bir alt kümesinin işlenmesi için bir kılavuz veya liste düzeninin sanallaştırılmasını düşünün.

Aşağıdaki `Virtualize` bileşen ( `Virtualize.cs` ), <xref:Microsoft.AspNetCore.Components.ComponentBase> Kullanıcı kaydırmasını temel alan alt içeriği işlemek için uygular:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

Aşağıdaki `FetchData` bileşen ( `FetchData.razor` ), `Virtualize` bir seferde 25 hava durumu verilerini göstermek için önceki bileşeni kullanır:

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

Yukarıdaki örnekte yaklaşım, her bir öğe için mutlak konumlandırmayı önlemenin bir öğesidir. Mutlak konumlandırma bazı avantajlara sahip olacaktır (öğelerin belirtilen miktarda Y alanı kaplamasını sağlar), ancak normal genişlikler kaybetmeniz ve tablo sütunlarının, içerik boyutlandırılmasına bağlı olarak satırlar/üst bilgi arasında satıra gelmesi için tablo sütunları alınamıyor olabilir.

Bileşenin tasarımının arkasındaki kavram, `Virtualize` BILEŞENIN Dom içinde öğelerin nasıl düzenlendiğini değiştirmez. Belirlediğiniz tek öğenin yanı sıra, eklenen sarmalayıcı öğe yoktur `TagName` .

En iyi yaklaşım sarmalayıcı öğesi bile kullanmaktan kaçınmaktır `TagName` . `Virtualize`Bileşene kendi öğelerinin kendisini yaymamalıdır. Tüm bileşenler işlenir, ancak en yakın kaydırılabilir üst öğede kalan görünür alanı doldurmanın yanı sıra, kaydırılabilir üst öğenin doğru kaydırma aralığına sahip olmasını sağlayan aşağıdaki bir yer işareti öğesi ekleyin. Düzen düşünüldüğünde, alt öğelerin tam aralığının DOM 'da fiziksel olması ile aynı olur. Bunun için doğru bir de belirtmeniz gerekir `ItemHeight` . Onu yanlış alırsanız (örneğin, karıştırılıyorsunuz ve üzerinde belirtmeye yönelik geçerli olduğunu düşünüyorsanız `style.height` `<tr>` ), bileşen yanlış sayıda şablon örneği oluşturmayı sonlandırır ve Kullanıcı arabirimini doldurmamalıdır ya da çok fazla sayıda işleme yok. Ayrıca, üst öğe üzerindeki kaydırma aralığı doğru olmayacaktır.

::: moniker-end

## <a name="state-changes"></a>Durum değişiklikleri

Bileşen tarafından işlenen öğelerde değişiklik yaparken `Virtualize` , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden değerlendirilmesini ve rerendering için çağrı yapın.
