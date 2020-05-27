---
<span data-ttu-id="4d18e-101">Başlık: ' ASP.NET Core Blazor şablonlu bileşenleri Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4d18e-101">title: 'ASP.NET Core Blazor templated components' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d18e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d18e-102">'Blazor'</span></span>
- <span data-ttu-id="4d18e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d18e-103">'Identity'</span></span>
- <span data-ttu-id="4d18e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d18e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d18e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d18e-105">'Razor'</span></span>
- <span data-ttu-id="4d18e-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4d18e-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="4d18e-107">BlazorŞablonlu bileşenleri ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d18e-107">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="4d18e-108">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="4d18e-108">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4d18e-109">Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-109">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="4d18e-110">Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-110">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="4d18e-111">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4d18e-111">A couple of examples include:</span></span>

* <span data-ttu-id="4d18e-112">Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="4d18e-112">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="4d18e-113">Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-113">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="4d18e-114">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="4d18e-114">Template parameters</span></span>

<span data-ttu-id="4d18e-115">Şablonlu bir bileşen, veya türünde bir veya daha fazla bileşen parametresi belirtilerek <xref:Microsoft.AspNetCore.Components.RenderFragment> tanımlanır <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="4d18e-115">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="4d18e-116">Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="4d18e-116">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="4d18e-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601>işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.</span><span class="sxs-lookup"><span data-stu-id="4d18e-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="4d18e-118">`TableTemplate`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="4d18e-118">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="4d18e-119">Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla ( `TableHeader` ve aşağıdaki örnekte) eşleşen alt öğeler kullanılarak belirtilebilir `RowTemplate` :</span><span class="sxs-lookup"><span data-stu-id="4d18e-119">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="4d18e-120">Genel tür kısıtlamaları sonraki sürümlerde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-120">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="4d18e-121">Daha fazla bilgi için bkz. [genel tür kısıtlamalarına Izin ver (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="4d18e-121">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="4d18e-122">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="4d18e-122">Template context parameters</span></span>

<span data-ttu-id="4d18e-123">Öğe olarak geçirilmiş türdeki bileşen bağımsız değişkenleri <xref:Microsoft.AspNetCore.Components.RenderFragment%601> adlı örtük bir parametreye sahiptir `context` (örneğin, yukarıdaki kod örneğinden `@context.PetId` ), ancak `Context` alt öğe üzerindeki özniteliğini kullanarak parametre adını değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d18e-123">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="4d18e-124">Aşağıdaki örnekte, `RowTemplate` öğesinin `Context` özniteliği `pet` parametresini belirtir:</span><span class="sxs-lookup"><span data-stu-id="4d18e-124">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="4d18e-125">Alternatif olarak, `Context` bileşen öğesi üzerinde özniteliğini de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d18e-125">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="4d18e-126">Belirtilen `Context` öznitelik, belirtilen tüm şablon parametreleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-126">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="4d18e-127">Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-127">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="4d18e-128">Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="4d18e-128">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="4d18e-129">Genel olarak yazılmış bileşenler</span><span class="sxs-lookup"><span data-stu-id="4d18e-129">Generic-typed components</span></span>

<span data-ttu-id="4d18e-130">Şablonlu bileşenler çoğunlukla genel olarak türdedir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-130">Templated components are often generically typed.</span></span> <span data-ttu-id="4d18e-131">Örneğin, `ListViewTemplate` değerleri işlemek için genel bir bileşen kullanılabilir `IEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="4d18e-131">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="4d18e-132">Genel bir bileşen tanımlamak için, [`@typeparam`](xref:mvc/views/razor#typeparam) tür parametrelerini belirtmek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4d18e-132">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="4d18e-133">Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:</span><span class="sxs-lookup"><span data-stu-id="4d18e-133">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="4d18e-134">Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4d18e-134">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="4d18e-135">Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:</span><span class="sxs-lookup"><span data-stu-id="4d18e-135">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
