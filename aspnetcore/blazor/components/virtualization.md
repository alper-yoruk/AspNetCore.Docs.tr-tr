---
title: ASP.NET Core Blazor bileşen Sanallaştırması
author: guardrex
description: ASP.NET Core uygulamalarda bileşen sanallaştırmayı nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-5.0'
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
ms.openlocfilehash: eafad420d72a974cc64ebfd6abb3eff2d73a115d
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805563"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor bileşen Sanallaştırması

[Daniel Roth](https://github.com/danroth27) tarafından

Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir Blazor . Sanallaştırma, UI işlemesini yalnızca şu anda görünür olan bölümlerle sınırlamak için bir tekniktir. Örneğin sanallaştırma, uygulamanın uzun bir öğe listesini işlemesi gerektiğinde ve belirli bir zamanda bir öğe alt kümesinin görünür olması gerektiğinde faydalıdır. Blazor`Virtualize`uygulamanın bileşenlerine sanallaştırma eklemek için kullanılabilecek bileşeni sağlar.

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

## <a name="state-changes"></a>Durum değişiklikleri

Bileşen tarafından işlenen öğelerde değişiklik yaparken `Virtualize` , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden değerlendirilmesini ve rerendering için çağrı yapın.
