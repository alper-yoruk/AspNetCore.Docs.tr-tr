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
ms.openlocfilehash: c2b16f4bc8e69265b1d59082c3f029541f277d8b
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252295"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor bileşen Sanallaştırması

[Daniel Roth](https://github.com/danroth27) tarafından

Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir Blazor . Sanallaştırma, UI işlemesini yalnızca şu anda görünür olan bölümlerle sınırlamak için bir tekniktir. Örneğin sanallaştırma, uygulamanın uzun bir öğe listesini işlemesi gerektiğinde ve belirli bir zamanda bir öğe alt kümesinin görünür olması gerektiğinde faydalıdır. Blazoruygulamanın bileşenlerine sanallaştırma eklemek için kullanılabilecek [ `Virtualize` bileşeni](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) sağlar.

`Virtualize`Bileşen şu durumlarda kullanılabilir:

* Bir döngüde veri öğeleri kümesi işleme.
* Öğelerin çoğu kaydırma nedeniyle görünür değil.
* İşlenen öğeler tam olarak aynı boyutta. Kullanıcı rastgele bir noktaya kaydığında, bileşen gösterilecek görünür öğeleri hesaplayabilir.

Sanallaştırma olmadan tipik bir liste, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) listedeki her öğeyi işlemek için bir C# döngüsü kullanabilir:

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Listede binlerce öğe varsa, listeyi işlemek uzun zaman alabilir. Kullanıcı, fark edilebilir bir UI gecikmesi yaşayabilir.

Listedeki her öğeyi tek seferde işlemek yerine, [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngüsünü `Virtualize` bileşeniyle değiştirin ve ile birlikte bir sabit öğe kaynağı belirtin <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Yalnızca şu anda görünür olan öğeler işlenir:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Bileşen için bir bağlam belirtmiyorsanız `Context` , `context` öğe içerik şablonundaki değeri kullanın:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> Öğelerin ve bileşenlerin model nesnelerinin eşleme süreci, [`@key`](xref:mvc/views/razor#key) Directive özniteliğiyle denetlenebilir. `@key` , anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur.
>
> Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [Razor ASP.NET Core için sözdizimi başvurusu](xref:mvc/views/razor#key)

`Virtualize`Bileşen:

* Kapsayıcının yüksekliğine ve işlenmiş öğelerin boyutuna göre işlenecek öğe sayısını hesaplar.
* Kullanıcı kaydırılırken öğeleri yeniden hesaplar ve tekrar kaydeder.
* Yalnızca geçerli görünür bölgeye karşılık gelen bir dış API 'den kayıt dilimini, koleksiyondaki tüm verileri indirmek yerine getirir.

Bileşen için öğe içeriği `Virtualize` şunları içerebilir:

* Yukarıdaki örnekte gösterildiği gibi düz HTML ve Razor kod.
* Bir veya daha fazla Razor bileşen.
* HTML/ Razor ve Razor bileşenleri karışımı.

## <a name="item-provider-delegate"></a>Öğe sağlayıcısı temsilcisi

Tüm öğeleri belleğe yüklemek istemiyorsanız, <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> istenen öğeleri istek üzerine zaman uyumsuz olarak alan bileşenin parametresine bir öğe sağlayıcısı temsilci yöntemi belirtebilirsiniz. Aşağıdaki örnekte, `LoadEmployees` yöntemi bileşene öğeleri sağlar `Virtualize` :

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Öğe sağlayıcısı <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , belirli bir başlangıç dizininden başlayarak gereken sayıda öğeyi belirten bir alır. Daha sonra öğe sağlayıcısı, istenen öğeleri bir veritabanından veya başka bir hizmetten alır ve bunları <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> Toplam öğe sayısı ile birlikte döndürür. Öğe sağlayıcısı her bir istek ile öğeleri almayı seçebilir veya bunları hazır olmaları için önbelleğe alabilir.

Bir `Virtualize` bileşen parametrelerden yalnızca **bir öğe kaynağını** kabul edebilir, bu nedenle bir öğe sağlayıcısını eşzamanlı olarak kullanmayı ve ' a bir koleksiyon atamayı denemeyin `Items` . Her ikisi de atanırsa, <xref:System.InvalidOperationException> bileşen parametreleri çalışma zamanında ayarlandığında bir oluşturulur.

Aşağıdaki `LoadEmployees` Yöntem örneği çalışanları bir öğesinden `EmployeeService` (gösterilmez) yükler:

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> bileşene verileri yeniden yönlendirme izni verir <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> . Bu, dış veriler değiştiğinde yararlıdır. Kullanırken bunu çağırmanız gerekmez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .

## <a name="placeholder"></a>Yer tutucu

Uzak bir veri kaynağından gelen öğelerin bir süre sürebileceğinden, öğe içeriğiyle bir yer tutucu oluşturma seçeneğiniz vardır:

* <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> `<Placeholder>...</Placeholder>` Öğe verileri kullanılabilir olana kadar içeriği göstermek için () kullanın.
* <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>Listenin öğe şablonunu ayarlamak için kullanın.

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

Her öğenin piksel cinsinden boyutu ayarlanabilir <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (varsayılan: 50):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Fazla tarama sayısı

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> görünen bölgeden önce ve sonra kaç tane ek öğenin işleneceğini belirler. Bu ayar, kaydırma sırasında işleme sıklığını azaltmaya yardımcı olur. Ancak, daha yüksek değerler sayfada işlenen daha fazla öğenin oluşmasına neden olur (varsayılan: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Durum değişiklikleri

Bileşen tarafından işlenen öğelerde değişiklik yaparken `Virtualize` , <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden değerlendirilmesini ve rerendering için çağrı yapın. Daha fazla bilgi için bkz. <xref:blazor/components/rendering>.
