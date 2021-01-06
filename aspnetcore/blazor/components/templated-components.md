---
title: BlazorŞablonlu bileşenleri ASP.NET Core
author: guardrex
description: Şablonlu bileşenlerin bir veya daha fazla kullanıcı arabirimi şablonunu parametre olarak kabul edip etmesinin, daha sonra bileşenin işleme mantığının bir parçası olarak kullanılabileceği hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: f818a0b7f1ba6d4dd6affeeba785c5e288568dd8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507960"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="96a3d-103">BlazorŞablonlu bileşenleri ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96a3d-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="96a3d-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="96a3d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="96a3d-105">Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="96a3d-106">Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="96a3d-107">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="96a3d-107">A couple of examples include:</span></span>

* <span data-ttu-id="96a3d-108">Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="96a3d-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="96a3d-109">Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="96a3d-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96a3d-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="96a3d-111">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="96a3d-111">Template parameters</span></span>

<span data-ttu-id="96a3d-112">Şablonlu bir bileşen, veya türünde bir veya daha fazla bileşen parametresi belirtilerek <xref:Microsoft.AspNetCore.Components.RenderFragment> tanımlanır <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="96a3d-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="96a3d-113">Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="96a3d-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="96a3d-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.</span><span class="sxs-lookup"><span data-stu-id="96a3d-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="96a3d-115">`TableTemplate` bileşen ( `TableTemplate.razor` ):</span><span class="sxs-lookup"><span data-stu-id="96a3d-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="96a3d-116">Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla ( `TableHeader` ve aşağıdaki örnekte) eşleşen alt öğeler kullanılarak belirtilebilir `RowTemplate` :</span><span class="sxs-lookup"><span data-stu-id="96a3d-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="96a3d-117">Genel tür kısıtlamaları sonraki sürümlerde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="96a3d-118">Daha fazla bilgi için bkz. [genel tür kısıtlamalarına Izin ver (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="96a3d-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="96a3d-119">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="96a3d-119">Template context parameters</span></span>

<span data-ttu-id="96a3d-120">Öğe olarak geçirilmiş türdeki bileşen bağımsız değişkenleri <xref:Microsoft.AspNetCore.Components.RenderFragment%601> adlı örtük bir parametreye sahiptir `context` (örneğin, yukarıdaki kod örneğinden `@context.PetId` ), ancak `Context` alt öğe üzerindeki özniteliğini kullanarak parametre adını değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="96a3d-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="96a3d-121">Aşağıdaki örnekte, `RowTemplate` öğesinin `Context` özniteliği `pet` parametresini belirtir:</span><span class="sxs-lookup"><span data-stu-id="96a3d-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

@code {
    ...
}
```

<span data-ttu-id="96a3d-122">Alternatif olarak, `Context` bileşen öğesi üzerinde özniteliğini de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="96a3d-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="96a3d-123">Belirtilen `Context` öznitelik, belirtilen tüm şablon parametreleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="96a3d-124">Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="96a3d-125">Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="96a3d-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="96a3d-126">Genel olarak yazılmış bileşenler</span><span class="sxs-lookup"><span data-stu-id="96a3d-126">Generic-typed components</span></span>

<span data-ttu-id="96a3d-127">Şablonlu bileşenler çoğunlukla genel olarak türdedir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-127">Templated components are often generically typed.</span></span> <span data-ttu-id="96a3d-128">Örneğin, `ListViewTemplate` değerleri işlemek için genel bir bileşen ( `ListViewTemplate.razor` ) kullanılabilir `IEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="96a3d-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="96a3d-129">Genel bir bileşen tanımlamak için, [`@typeparam`](xref:mvc/views/razor#typeparam) tür parametrelerini belirtmek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="96a3d-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="96a3d-130">Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:</span><span class="sxs-lookup"><span data-stu-id="96a3d-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="96a3d-131">Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="96a3d-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="96a3d-132">Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:</span><span class="sxs-lookup"><span data-stu-id="96a3d-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
