---
title: ASP.NET Blazor Çekirdek yaşam döngüsü
author: guardrex
description: ASP.NET Core Blazor uygulamalarında Razor bileşen yaşam döngüsü yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791461"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Blazor Çekirdek yaşam döngüsü

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Çerçeve Blazor senkron ve eşzamanlı yaşam döngüsü yöntemlerini içerir. Bileşen başlatma ve işleme sırasında bileşenler üzerinde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.

## <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

### <a name="component-initialization-methods"></a>Bileşen başlatma yöntemleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> bileşen, ana bileşeninden ilk parametrelerini aldıktan sonra başlatıldığında çağrılır. Bileşen `OnInitializedAsync` bir eşzamanlı işlem gerçekleştirirken kullanın ve işlem tamamlandığında yenilemeniz gerekir.

Senkron bir işlem için `OnInitialized`geçersiz kılma:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Eşzamanlı bir işlem gerçekleştirmek için, `OnInitializedAsync` işlemdeki `await` anahtar kelimeyi geçersiz kılın ve kullanın:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor[İçeriklerini](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_iki kez_** önceden arayan sunucu uygulamaları:

* Bileşen ilk olarak sayfanın bir parçası olarak statik olarak işlendiğinde bir kez.
* Tarayıcı sunucuya bir bağlantı kurduğunda ikinci kez.

Geliştirici kodunun `OnInitializedAsync` iki kez çalışmasını önlemek için, ön işleme bölümünden [sonra Stateful yeniden bağlantısına](#stateful-reconnection-after-prerendering) bakın.

Sunucu Blazor uygulaması önceden işlenirken, tarayıcıyla bağlantı kurulmadığı ndan, JavaScript'e arama gibi belirli eylemler mümkün değildir. Bileşenlerin önceden işlendiğinde farklı işlemesi gerekebilir. Daha fazla bilgi için, uygulamanın ön oluşturma bölümü [ne zaman algıla'ya](#detect-when-the-app-is-prerendering) bakın.

Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır. Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="before-parameters-are-set"></a>Parametreler ayarlı önce

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>oluşturma ağacında bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>her seferinde `SetParametersAsync` parametre değerlerinin tüm kümesini içerir.

Varsayılan uygulama, `SetParametersAsync` her özelliğin değerini, `[Parameter]` `[CascadingParameter]` `ParameterView`'de karşılık gelen bir değere sahip olan veya özniteliği olan' a göre ayarlar. Karşılık gelen değeri `ParameterView` olmayan parametreler değişmeden bırakılır.

`base.SetParametersAync` Çağrılmazsa, özel kod gelen parametrelerin değerini gereken şekilde yorumlayabilir. Örneğin, gelen parametreleri sınıftaki özelliklere atama zorunluluğu yoktur.

Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır. Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-parameters-are-set"></a>Parametreler ayarlandıktan sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> denir:

* Bileşen baş harfe döndüğünde ve ilk parametre kümesini ana bileşeninden aldığında.
* Ana bileşen yeniden işleyip sağladığında:
  * Yalnızca bilinen ilkel değişmez türleri en az bir parametre değişti.
  * Karmaşık daktio parametreler. Çerçeve, karmaşık daktinolu parametredeğerlerinin dahili olarak mutasyona uğrayıp çözülmediğini bilemez, bu nedenle parametre kümesini değiştirilmiş olarak değerlendirir.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Parametreler ilerlerken eşzamanlı çalışma ve özellik değerleri `OnParametersSetAsync` yaşam döngüsü olayı sırasında oluşmalıdır.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır. Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-component-render"></a>Bileşen işlemeden sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> bir bileşen işleme bittikten sonra çağrılır. Öğe ve bileşen başvuruları bu noktada doldurulur. İşlenen DOM öğeleri üzerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenen içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.

Parametresi `firstRender` `OnAfterRenderAsync` için `OnAfterRender`ve:

* Bileşen `true` örneğinin ilk kez görüntülenmidir.
* Başlatma çalışmasının yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> İşlemeden hemen sonra asenkron çalışma `OnAfterRenderAsync` yaşam döngüsü olayı sırasında gerçekleşmelidir.
>
> Bir <xref:System.Threading.Tasks.Task> geri `OnAfterRenderAsync`dönseniz bile, çerçeve, bu görev tamamlandıktan sonra bileşeniniz için başka bir işleme döngüsü zamanlamıyor. Bu sonsuz bir render döngü önlemek içindir. Döndürülen görev tamamlandıktan sonra başka bir işleme döngüsü zamanlayan diğer yaşam döngüsü yöntemlerinden farklıdır.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender``OnAfterRenderAsync` *ve sunucuda önişleme çağrıldığında.*

Herhangi bir olay işleyicisi ayarlanırsa, bunları imha da kaldır. Daha fazla bilgi [için, IDisposable bölümüyle Bileşen elden çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="suppress-ui-refreshing"></a>UI'yi yenilemeyi bastırma

UI <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> yenileme bastırmak için geçersiz kılma. Uygulama dönerse, `true`UI yenilenir:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`bileşen her işlendiher zaman denir.

Geçersiz `ShouldRender` kılınmış olsa bile, bileşen her zaman başlangıçta işlenir.

## <a name="state-changes"></a>Durum değişiklikleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>durumunun değiştiğini fark eder. Gerektiğinde, arama `StateHasChanged` bileşeniyeniden oluşturulabilir neden olur.

## <a name="handle-incomplete-async-actions-at-render"></a>İşleme de tamamlanmamış async eylemleri işleme

Yaşam döngüsü olaylarında gerçekleştirilen eşsenkronize eylemler, bileşen oluşturulmadan önce tamamlanmamış olabilir. Nesneler, yaşam `null` döngüsü yöntemi yürütülerken verilerle dolu olabilir veya tam olarak doldurulabilir. Nesnelerin baş harfe başlatOlduğunu doğrulamak için işleme mantığı sağlayın. Nesneler `null`.

Şablonların `FetchData` bileşeninde, `OnInitializedAsync` tahmin verilerini asychronously almak için geçersiz`forecasts`kılınan ( ). Blazor Ne `forecasts` `null`zaman , bir yükleme iletisi kullanıcıya görüntülenir. Döndürülen `Task` tamamlandıktan `OnInitializedAsync` sonra, bileşen güncelleştirilmiş durumla yeniden işlenir.

Sunucu şablonundaki Blazor *Sayfalar/FetchData.razor:*

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable ile bileşen imhası

Bir bileşen uygularsa, <xref:System.IDisposable>bileşen UI'den kaldırıldığında Elden Çıkarma [yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağrılır. Aşağıdaki bileşen `@implements IDisposable` kullanır `Dispose` ve yöntem:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> Arama <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` desteklenmez. `StateHasChanged`işleyiciyi yıkmanın bir parçası olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmeleri istenmesi desteklenmez.

.NET olaylarından olay işleyicilerinin aboneliğini iptal edin. Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yöntemde bir olay işleyicisinin nasıl çözülecek olduğunu `Dispose` gösterir:

* Özel alan ve lambda yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Özel yöntem yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Hataları işleme

Yaşam döngüsü yöntemi yürütme sırasında hataları <xref:blazor/handle-errors#lifecycle-methods>işleme hakkında bilgi için bkz.

## <a name="stateful-reconnection-after-prerendering"></a>Önişleme den sonra durumlu yeniden bağlantı

Sunucu Blazor `RenderMode` `ServerPrerendered`uygulamasında, bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir. Tarayıcı sunucuya bir bağlantı kurduktan sonra, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur. Bileşeni başlatmaya yönelik [OnInitialized{Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:

* Bileşen statik olarak önceden işlendiğinde.
* Sunucu bağlantısı kurulduktan sonra.

Bu, bileşen sonunda işlendiğinde UI'de görüntülenen verilerde gözle görülür bir değişikliğe neden olabilir.

Bir Blazor Server uygulamasında çift işleme senaryosunu önlemek için:

* Ön işleme sırasında durumu önbelleğe almak ve uygulama yeniden başlatıldıktan sonra durumu almak için kullanılabilecek bir tanımlayıcıyı geçirin.
* Bileşen durumunu kaydetmek için ön işleme sırasında tanımlayıcıyı kullanın.
* Önbelleğe alınan durumu almak için ön işlemeden sonra tanımlayıcıyı kullanın.

Aşağıdaki kod, çift `WeatherForecastService` işlemeyi önleyen Blazor şablon tabanlı bir Sunucu uygulamasında güncelleştirilmiş bir kod gösterir:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Hakkında daha fazla `RenderMode`bilgi <xref:blazor/hosting-model-configuration#render-mode>için bkz.

## <a name="detect-when-the-app-is-prerendering"></a>Uygulamanın ne zaman önceden oluşturulaca-son veriş olduğunu algılama

[!INCLUDE[](~/includes/blazor-prerendering.md)]
