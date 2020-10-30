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
ms.openlocfilehash: ee38040a1edc8db43b8d5bf0202fd519b153ea04
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056250"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a>BlazorŞablonlu bileşenleri ASP.NET Core

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir. Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir. Birkaç örnek şunlardır:

* Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.
* Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>Şablon parametreleri

Şablonlu bir bileşen, veya türünde bir veya daha fazla bileşen parametresi belirtilerek <xref:Microsoft.AspNetCore.Components.RenderFragment> tanımlanır <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.

`TableTemplate` bileşen ( `TableTemplate.razor` ):

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla ( `TableHeader` ve aşağıdaki örnekte) eşleşen alt öğeler kullanılarak belirtilebilir `RowTemplate` :

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
> Genel tür kısıtlamaları sonraki sürümlerde desteklenecektir. Daha fazla bilgi için bkz. [genel tür kısıtlamalarına Izin ver (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Şablon bağlam parametreleri

Öğe olarak geçirilmiş türdeki bileşen bağımsız değişkenleri <xref:Microsoft.AspNetCore.Components.RenderFragment%601> adlı örtük bir parametreye sahiptir `context` (örneğin, yukarıdaki kod örneğinden `@context.PetId` ), ancak `Context` alt öğe üzerindeki özniteliğini kullanarak parametre adını değiştirebilirsiniz. Aşağıdaki örnekte, `RowTemplate` öğesinin `Context` özniteliği `pet` parametresini belirtir:

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

Alternatif olarak, `Context` bileşen öğesi üzerinde özniteliğini de belirtebilirsiniz. Belirtilen `Context` öznitelik, belirtilen tüm şablon parametreleri için geçerlidir. Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir. Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:

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

## <a name="generic-typed-components"></a>Genel olarak yazılmış bileşenler

Şablonlu bileşenler çoğunlukla genel olarak türdedir. Örneğin, `ListViewTemplate` değerleri işlemek için genel bir bileşen ( `ListViewTemplate.razor` ) kullanılabilir `IEnumerable<T>` . Genel bir bileşen tanımlamak için, [`@typeparam`](xref:mvc/views/razor#typeparam) tür parametrelerini belirtmek için yönergesini kullanın:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:

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

Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir. Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:

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
