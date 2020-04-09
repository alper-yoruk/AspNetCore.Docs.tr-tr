---
title: ASP.NET Blazor Core şablonbileşenleri
author: guardrex
description: Şablonlanmış bileşenlerin bir veya daha fazla UI şablonlarını parametre olarak nasıl kabul edebileceğini ve bu şablonun oluşturma mantığının bir parçası olarak nasıl kullanılabileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989494"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="9a1cf-103">ASP.NET Blazor Core şablonbileşenleri</span><span class="sxs-lookup"><span data-stu-id="9a1cf-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="9a1cf-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9a1cf-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9a1cf-105">Şablonbileşenleri, bir veya daha fazla UI şablonlarını parametre olarak kabul eden ve daha sonra bileşenin oluşturma mantığının bir parçası olarak kullanılabilecek bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="9a1cf-106">Şablonlu bileşenler, normal bileşenlerden daha kullanılabilir olan daha üst düzey bileşenler yazmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="9a1cf-107">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-107">A couple of examples include:</span></span>

* <span data-ttu-id="9a1cf-108">Kullanıcının tablonun üstbilgisi, satırları ve altbilgiiçin şablonlar belirtmesine olanak tanıyan tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="9a1cf-109">Kullanıcının listedeki öğeleri işlemek için şablon belirtmesine olanak tanıyan bir liste bileşeni.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="9a1cf-110">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="9a1cf-110">Template parameters</span></span>

<span data-ttu-id="9a1cf-111">Şablonlanmış bileşen, bir `RenderFragment` veya daha fazla bileşen parametresi belirtilerek tanımlanır. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="9a1cf-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="9a1cf-112">İşleme parçası, işlenebilmek için ui'nin bir kesimini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="9a1cf-113">`RenderFragment<T>`render parçası çağrıldığızaman belirtilebilen bir tür parametresi alır.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="9a1cf-114">`TableTemplate`Bileşen:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="9a1cf-115">Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla eşleşen`TableHeader` alt `RowTemplate` öğeler kullanılarak belirtilebilir (ve aşağıdaki örnekte):</span><span class="sxs-lookup"><span data-stu-id="9a1cf-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="9a1cf-116">Genel tür kısıtlamaları gelecekteki bir sürümde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="9a1cf-117">Daha fazla bilgi için bkz. [genel tür kısıtlamalarına izin ver (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="9a1cf-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="9a1cf-118">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="9a1cf-118">Template context parameters</span></span>

<span data-ttu-id="9a1cf-119">Öğeler olarak `RenderFragment<T>` geçirilen tür bileşeni bağımsız değişkenler adında `context` örtülü bir parametreye sahiptir (örneğin önceki kod örneğinden), `@context.PetId`ancak alt öğedeki `Context` özniteliği kullanarak parametre adını değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="9a1cf-120">Aşağıdaki örnekte, `RowTemplate` öğenin `Context` özniteliği parametreyi `pet` belirtir:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="9a1cf-121">Alternatif olarak, bileşen `Context` öğesiüzerinde öznitelik belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="9a1cf-122">Belirtilen `Context` öznitelik tüm belirtilen şablon parametreleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="9a1cf-123">Bu, örtülü alt içerik için içerik parametre adını belirtmek istediğinizde (herhangi bir kaydırma alt öğesi olmadan) yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="9a1cf-124">Aşağıdaki örnekte, `Context` öznitelik öğeüzerinde `TableTemplate` görünür ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="9a1cf-125">Genel dakti-yazılı bileşenler</span><span class="sxs-lookup"><span data-stu-id="9a1cf-125">Generic-typed components</span></span>

<span data-ttu-id="9a1cf-126">Şablondaki bileşenler genellikle genel olarak dizilir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-126">Templated components are often generically typed.</span></span> <span data-ttu-id="9a1cf-127">Örneğin, değerleri `ListViewTemplate` işlemek `IEnumerable<T>` için genel bir bileşen kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="9a1cf-128">Genel bir bileşen tanımlamak [`@typeparam`](xref:mvc/views/razor#typeparam) için, tür parametrelerini belirtmek için yönergeyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="9a1cf-129">Genel olarak yazılan bileşenleri kullanırken, tür parametresi mümkünse çıkarılır:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="9a1cf-130">Aksi takdirde, tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9a1cf-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="9a1cf-131">Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:</span><span class="sxs-lookup"><span data-stu-id="9a1cf-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
