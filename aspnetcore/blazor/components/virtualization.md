---
title: ASP.NET Core Blazor bileşen Sanallaştırması
author: guardrex
description: ASP.NET Core uygulamalarda bileşen sanallaştırmayı nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 051721b62397b582f1ffdaba08ffefe5d0c9ae03
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972021"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="d5097-103">ASP.NET Core Blazor bileşen Sanallaştırması</span><span class="sxs-lookup"><span data-stu-id="d5097-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="d5097-104">[Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="d5097-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d5097-105">Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir Blazor .</span><span class="sxs-lookup"><span data-stu-id="d5097-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="d5097-106">Sanallaştırma, UI işlemesini yalnızca şu anda görünür olan bölümlerle sınırlamak için bir tekniktir.</span><span class="sxs-lookup"><span data-stu-id="d5097-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="d5097-107">Örneğin sanallaştırma, uygulamanın uzun bir öğe listesini işlemesi gerektiğinde ve belirli bir zamanda bir öğe alt kümesinin görünür olması gerektiğinde faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5097-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="d5097-108">Blazoruygulamanın bileşenlerine sanallaştırma eklemek için kullanılabilecek [ `Virtualize` bileşeni](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5097-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="d5097-109">Sanallaştırma olmadan tipik bir liste, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) listedeki her öğeyi işlemek için bir C# döngüsü kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="d5097-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="d5097-110">Listede binlerce öğe varsa, listeyi işlemek uzun zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d5097-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="d5097-111">Kullanıcı, fark edilebilir bir UI gecikmesi yaşayabilir.</span><span class="sxs-lookup"><span data-stu-id="d5097-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="d5097-112">Listedeki her öğeyi tek seferde işlemek yerine, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngüsünü `Virtualize` bileşeniyle değiştirin ve ile birlikte bir sabit öğe kaynağı belirtin <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d5097-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d5097-113">Yalnızca şu anda görünür olan öğeler işlenir:</span><span class="sxs-lookup"><span data-stu-id="d5097-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d5097-114">Bileşen için bir bağlam belirtmiyorsanız `Context` , `context` `@context.{PROPERTY}` öğe içerik şablonunda () değerini kullanın:</span><span class="sxs-lookup"><span data-stu-id="d5097-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d5097-115">`Virtualize`Bileşen kapsayıcının yüksekliğine ve işlenmiş öğelerin boyutuna göre işlenecek öğe sayısını hesaplar.</span><span class="sxs-lookup"><span data-stu-id="d5097-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="d5097-116">Bileşen için öğe içeriği `Virtualize` şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="d5097-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="d5097-117">Yukarıdaki örnekte gösterildiği gibi düz HTML ve Razor kod.</span><span class="sxs-lookup"><span data-stu-id="d5097-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="d5097-118">Bir veya daha fazla Razor bileşen.</span><span class="sxs-lookup"><span data-stu-id="d5097-118">One or more Razor components.</span></span>
* <span data-ttu-id="d5097-119">HTML/ Razor ve Razor bileşenleri karışımı.</span><span class="sxs-lookup"><span data-stu-id="d5097-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="d5097-120">Öğe sağlayıcısı temsilcisi</span><span class="sxs-lookup"><span data-stu-id="d5097-120">Item provider delegate</span></span>

<span data-ttu-id="d5097-121">Tüm öğeleri belleğe yüklemek istemiyorsanız, <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> istenen öğeleri istek üzerine zaman uyumsuz olarak alan bileşenin parametresine bir öğe sağlayıcısı temsilci yöntemi belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d5097-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d5097-122">Öğe sağlayıcısı <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , belirli bir başlangıç dizininden başlayarak gereken sayıda öğeyi belirten bir alır.</span><span class="sxs-lookup"><span data-stu-id="d5097-122">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="d5097-123">Daha sonra öğe sağlayıcısı, istenen öğeleri bir veritabanından veya başka bir hizmetten alır ve bunları <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> Toplam öğe sayısı ile birlikte döndürür.</span><span class="sxs-lookup"><span data-stu-id="d5097-123">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="d5097-124">Öğe sağlayıcısı her bir istek ile öğeleri almayı seçebilir veya bunları hazır olmaları için önbelleğe alabilir.</span><span class="sxs-lookup"><span data-stu-id="d5097-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="d5097-125">Bir `Virtualize` bileşen parametrelerden yalnızca **bir öğe kaynağını** kabul edebilir, bu nedenle bir öğe sağlayıcısını eşzamanlı olarak kullanmayı ve ' a bir koleksiyon atamayı denemeyin `Items` .</span><span class="sxs-lookup"><span data-stu-id="d5097-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="d5097-126">Her ikisi de atanırsa, <xref:System.InvalidOperationException> bileşen parametreleri çalışma zamanında ayarlandığında bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d5097-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="d5097-127">Aşağıdaki örnek, çalışanları bir ile yükler `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="d5097-127">The following example loads employees from an `EmployeeService`:</span></span>

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

<span data-ttu-id="d5097-128"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> bileşene verileri yeniden yönlendirme izni verir <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5097-128"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="d5097-129">Bu, dış veriler değiştiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d5097-129">This is useful when external data changes.</span></span> <span data-ttu-id="d5097-130">Kullanırken bunu çağırmanız gerekmez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5097-130">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="d5097-131">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="d5097-131">Placeholder</span></span>

<span data-ttu-id="d5097-132">Uzak bir veri kaynağından gelen öğelerin bir süre sürebileceğinden, öğe içeriğiyle bir yer tutucu oluşturma seçeneğiniz vardır:</span><span class="sxs-lookup"><span data-stu-id="d5097-132">Because requesting items from a remote data source might take some time, you have the option to render a placeholder  with item content:</span></span>

* <span data-ttu-id="d5097-133"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> `<Placeholder>...</Placeholder>` Öğe verileri kullanılabilir olana kadar içeriği göstermek için () kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5097-133">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="d5097-134"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>Listenin öğe şablonunu ayarlamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d5097-134">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="d5097-135">Öğe boyutu</span><span class="sxs-lookup"><span data-stu-id="d5097-135">Item size</span></span>

<span data-ttu-id="d5097-136">Her öğenin piksel cinsinden boyutu ayarlanabilir <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (varsayılan: 50px):</span><span class="sxs-lookup"><span data-stu-id="d5097-136">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="d5097-137">Fazla tarama sayısı</span><span class="sxs-lookup"><span data-stu-id="d5097-137">Overscan count</span></span>

<span data-ttu-id="d5097-138"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> görünen bölgeden önce ve sonra kaç tane ek öğenin işleneceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="d5097-138"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="d5097-139">Bu ayar, kaydırma sırasında işleme sıklığını azaltmaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d5097-139">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="d5097-140">Ancak, daha yüksek değerler sayfada işlenen daha fazla öğenin oluşmasına neden olur (varsayılan: 3):</span><span class="sxs-lookup"><span data-stu-id="d5097-140">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="d5097-141">Durum değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="d5097-141">State changes</span></span>

<span data-ttu-id="d5097-142">Bileşen tarafından işlenen öğelerde değişiklik yaparken `Virtualize` , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden değerlendirilmesini ve rerendering için çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="d5097-142">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
