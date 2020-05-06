---
title: Şablonlu bileşenleri ASP.NET Core Blazor
author: guardrex
description: Şablonlu bileşenlerin bir veya daha fazla kullanıcı arabirimi şablonunu parametre olarak kabul edip etmesinin, daha sonra bileşenin işleme mantığının bir parçası olarak kullanılabileceği hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771073"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="9737b-103">Şablonlu bileşenleri ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9737b-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="9737b-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="9737b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9737b-105">Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9737b-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="9737b-106">Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="9737b-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="9737b-107">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9737b-107">A couple of examples include:</span></span>

* <span data-ttu-id="9737b-108">Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="9737b-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="9737b-109">Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="9737b-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="9737b-110">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="9737b-110">Template parameters</span></span>

<span data-ttu-id="9737b-111">Şablonlu bir bileşen, veya `RenderFragment` `RenderFragment<T>`türünde bir veya daha fazla bileşen parametresi belirtilerek tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="9737b-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="9737b-112">Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9737b-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="9737b-113">`RenderFragment<T>`işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.</span><span class="sxs-lookup"><span data-stu-id="9737b-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="9737b-114">`TableTemplate`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="9737b-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="9737b-115">Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla (`TableHeader` ve `RowTemplate` aşağıdaki örnekte) eşleşen alt öğeler kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="9737b-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="9737b-116">Genel tür kısıtlamaları sonraki sürümlerde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="9737b-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="9737b-117">Daha fazla bilgi için bkz. [genel tür kısıtlamalarına Izin ver (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="9737b-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="9737b-118">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="9737b-118">Template context parameters</span></span>

<span data-ttu-id="9737b-119">Öğe olarak geçirilmiş türdeki `RenderFragment<T>` bileşen bağımsız değişkenleri adlı `context` örtük bir parametreye sahiptir (örneğin, `@context.PetId`Yukarıdaki kod örneğinden), ancak alt öğe üzerindeki `Context` özniteliğini kullanarak parametre adını değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9737b-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="9737b-120">Aşağıdaki örnekte, `RowTemplate` öğesinin `Context` özniteliği `pet` parametresini belirtir:</span><span class="sxs-lookup"><span data-stu-id="9737b-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="9737b-121">Alternatif olarak, bileşen öğesi üzerinde `Context` özniteliğini de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9737b-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="9737b-122">Belirtilen `Context` öznitelik, belirtilen tüm şablon parametreleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9737b-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="9737b-123">Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="9737b-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="9737b-124">Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="9737b-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="9737b-125">Genel olarak yazılmış bileşenler</span><span class="sxs-lookup"><span data-stu-id="9737b-125">Generic-typed components</span></span>

<span data-ttu-id="9737b-126">Şablonlu bileşenler çoğunlukla genel olarak türdedir.</span><span class="sxs-lookup"><span data-stu-id="9737b-126">Templated components are often generically typed.</span></span> <span data-ttu-id="9737b-127">Örneğin, değerleri işlemek `ListViewTemplate` `IEnumerable<T>` için genel bir bileşen kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9737b-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="9737b-128">Genel bir bileşen tanımlamak için, tür parametrelerini [`@typeparam`](xref:mvc/views/razor#typeparam) belirtmek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9737b-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="9737b-129">Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:</span><span class="sxs-lookup"><span data-stu-id="9737b-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="9737b-130">Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9737b-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="9737b-131">Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:</span><span class="sxs-lookup"><span data-stu-id="9737b-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
