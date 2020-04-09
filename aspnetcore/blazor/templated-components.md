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
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET Blazor Core şablonbileşenleri

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Şablonbileşenleri, bir veya daha fazla UI şablonlarını parametre olarak kabul eden ve daha sonra bileşenin oluşturma mantığının bir parçası olarak kullanılabilecek bileşenlerdir. Şablonlu bileşenler, normal bileşenlerden daha kullanılabilir olan daha üst düzey bileşenler yazmanıza olanak sağlar. Birkaç örnek şunlardır:

* Kullanıcının tablonun üstbilgisi, satırları ve altbilgiiçin şablonlar belirtmesine olanak tanıyan tablo bileşeni.
* Kullanıcının listedeki öğeleri işlemek için şablon belirtmesine olanak tanıyan bir liste bileşeni.

## <a name="template-parameters"></a>Şablon parametreleri

Şablonlanmış bileşen, bir `RenderFragment` veya daha fazla bileşen parametresi belirtilerek tanımlanır. `RenderFragment<T>` İşleme parçası, işlenebilmek için ui'nin bir kesimini temsil eder. `RenderFragment<T>`render parçası çağrıldığızaman belirtilebilen bir tür parametresi alır.

`TableTemplate`Bileşen:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla eşleşen`TableHeader` alt `RowTemplate` öğeler kullanılarak belirtilebilir (ve aşağıdaki örnekte):

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
> Genel tür kısıtlamaları gelecekteki bir sürümde desteklenir. Daha fazla bilgi için bkz. [genel tür kısıtlamalarına izin ver (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Şablon bağlam parametreleri

Öğeler olarak `RenderFragment<T>` geçirilen tür bileşeni bağımsız değişkenler adında `context` örtülü bir parametreye sahiptir (örneğin önceki kod örneğinden), `@context.PetId`ancak alt öğedeki `Context` özniteliği kullanarak parametre adını değiştirebilirsiniz. Aşağıdaki örnekte, `RowTemplate` öğenin `Context` özniteliği parametreyi `pet` belirtir:

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

Alternatif olarak, bileşen `Context` öğesiüzerinde öznitelik belirtebilirsiniz. Belirtilen `Context` öznitelik tüm belirtilen şablon parametreleri için geçerlidir. Bu, örtülü alt içerik için içerik parametre adını belirtmek istediğinizde (herhangi bir kaydırma alt öğesi olmadan) yararlı olabilir. Aşağıdaki örnekte, `Context` öznitelik öğeüzerinde `TableTemplate` görünür ve tüm şablon parametreleri için geçerlidir:

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

## <a name="generic-typed-components"></a>Genel dakti-yazılı bileşenler

Şablondaki bileşenler genellikle genel olarak dizilir. Örneğin, değerleri `ListViewTemplate` işlemek `IEnumerable<T>` için genel bir bileşen kullanılabilir. Genel bir bileşen tanımlamak [`@typeparam`](xref:mvc/views/razor#typeparam) için, tür parametrelerini belirtmek için yönergeyi kullanın:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Genel olarak yazılan bileşenleri kullanırken, tür parametresi mümkünse çıkarılır:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Aksi takdirde, tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir. Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
