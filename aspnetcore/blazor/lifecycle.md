---
Başlık: ' ASP.NET Core Blazor yaşam döngüsü ' Yazar: Açıklama: ' Razor ASP.NET Core uygulamalarda bileşen yaşam döngüsü yöntemlerini kullanmayı öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-lifecycle"></a>ASP.NET Core Blazor yaşam döngüsü

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

BlazorÇerçeve, zaman uyumlu ve zaman uyumsuz yaşam döngüsü yöntemlerini içerir. Bileşen başlatma ve işleme sırasında bileşenlerde ek işlemler gerçekleştirmek için yaşam döngüsü yöntemlerini geçersiz kılın.

## <a name="lifecycle-methods"></a>Yaşam döngüsü yöntemleri

### <a name="component-initialization-methods"></a>Bileşen başlatma yöntemleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>ve, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> bileşen ilk parametrelerini ana bileşeninden aldıktan sonra başlatıldığında çağrılır. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Bileşen zaman uyumsuz bir işlem gerçekleştirdiğinde ve işlem tamamlandığında yenilenmesi gerektiğinde kullanın.

Zaman uyumlu bir işlem için, geçersiz kılın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Zaman uyumsuz bir işlem gerçekleştirmek için, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> işlem üzerinde [await](/dotnet/csharp/language-reference/operators/await) işlecini geçersiz kılın ve kullanın:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor[İçerik](xref:blazor/hosting-model-configuration#render-mode) araması yapan sunucu uygulamaları <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_iki kez_**.

* Bir kez, bileşen sayfanın bir parçası olarak başlangıçta statik olarak işlendiğinde.
* Tarayıcı sunucuya geri bir bağlantı kurduğunda ikinci bir zaman.

İçindeki geliştirici kodunun <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> iki kez çalıştırılmasını engellemek için [prerendering sonrasında durum bilgisi olan yeniden bağlanma](#stateful-reconnection-after-prerendering) bölümüne bakın.

BlazorSunucu uygulaması prerendering olsa da, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir. Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir. Daha fazla bilgi için bkz. [uygulamanın ne zaman prerendering](#detect-when-the-app-is-prerendering) bölümüne bakın.

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="before-parameters-are-set"></a>Parametreler ayarlanmadan önce

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>işleme ağacındaki bileşenin üst öğesi tarafından sağlanan parametreleri ayarlar:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>her çağrılışında parametre değerleri kümesinin tamamını içerir <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .

Varsayılan uygulama <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> , her bir özelliğin değerini [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) içinde karşılık gelen bir değere sahip veya özniteliğiyle ayarlar <xref:Microsoft.AspNetCore.Components.ParameterView> . İçinde karşılık gelen bir değere sahip olmayan parametreler <xref:Microsoft.AspNetCore.Components.ParameterView> değişmeden bırakılır.

Eğer [tabanı. SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) çağrılmazsa, özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir. Örneğin, sınıftaki özelliklere gelen parametreleri atama gereksinimi yoktur.

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-parameters-are-set"></a>Parametreler ayarlandıktan sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> Şu şekilde adlandırılır:

* Bileşen başlatıldığında ve üst bileşeninden ilk parametre kümesini aldığında.
* Üst bileşen yeniden oluşturup şunları sağlar:
  * Yalnızca en az bir parametresinin değiştiği, bilinen temel sabit türler.
  * Tüm karmaşık türsüz parametreler. Çerçeve, karmaşık yazılmış bir parametrenin değerlerinin dahili olarak değişip değişmediğini bilmez, bu yüzden parametre kümesini değiştirilmiş olarak değerlendirir.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Yaşam döngüsü olayında parametre ve özellik değerleri uygulanırken zaman uyumsuz iş olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> .

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="after-component-render"></a>Bileşen oluşturulduktan sonra

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> bir bileşen işlemeyi tamamladıktan sonra çağrılır. Öğe ve bileşen başvuruları bu noktada doldurulur. İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.

`firstRender`Ve için parametresi <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

* , `true` Bileşen örneği ilk kez işlendiğinde olarak ayarlanır.
* Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olmak için kullanılabilir.

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
> Yaşam döngüsü olayında işleme hemen sonra zaman uyumsuz çalışma gerçekleşmelidir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .
>
> Bir öğesinden döndürseniz bile <xref:System.Threading.Tasks.Task> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , çerçeve bu görev tamamlandıktan sonra bileşen için başka bir işleme çevrimi zamanlayamaz. Bu, sonsuz bir işleme döngüsünden kaçınmaktır. Diğer yaşam döngüsü yöntemlerinden farklı olduğundan, döndürülen görev tamamlandığında daha fazla işleme döngüsü zamanlayabilirsiniz.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>ve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *sunucuda prerendering çağrıldığında çağrılmaz.*

Herhangi bir olay işleyicisi ayarlandıysa, bunların aktiften çıkarılmasını geri alır. Daha fazla bilgi için, [IDisposable Ile bileşen aktiften çıkarma](#component-disposal-with-idisposable) bölümüne bakın.

### <a name="suppress-ui-refreshing"></a>UI yenilemeyi bastır

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>UI yenilemeyi gizlemek için geçersiz kılın. Uygulama döndürürse `true` , Kullanıcı arabirimi yenilenir:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>bileşen her işlendiğinde çağrılır.

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.

Daha fazla bilgi için bkz. <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.

## <a name="state-changes"></a>Durum değişiklikleri

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>bileşene durumunun değiştiğini bildirir. Uygun olduğunda, çağırma <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bileşenin yeniden yönlendirilmesine neden olur.

## <a name="handle-incomplete-async-actions-at-render"></a>İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle

Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir. `null`Yaşam döngüsü yöntemi yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir. Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın. Nesneler olduğunda yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme `null` .

`FetchData` Blazor Şablonların bileşeninde, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Asychronously () tahmin verileri almak için geçersiz kılınır `forecasts` . Ne zaman olduğunda `forecasts` `null` , kullanıcıya bir yükleme iletisi görüntülenir. `Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> İşlem tamamlandıktan sonra, bileşen güncelleştirilmiş duruma geri döner.

Sunucu şablonunda *Pages/FetchData. Razor* Blazor :

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable ile bileşen atma

Bir bileşen uygularsa <xref:System.IDisposable> , bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağırılır. Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemini kullanır:

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
> <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>' In çağrılması `Dispose` desteklenmez. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>oluşturucuyu aşağı doğru olarak çağrılabilir, bu nedenle bu noktada UI güncelleştirmelerinin kullanılması desteklenmez.

.NET etkinliklerinden olay işleyicilerini kaldırma. Aşağıdaki [ Blazor form](xref:blazor/forms-validation) örnekleri, yönteminde bir olay işleyicisinin nasıl geri yükleneceğini göstermektedir `Dispose` :

* Özel alan ve lambda yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Özel yöntem yaklaşımı

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Hataları işleme

Yaşam döngüsü yöntemi yürütme sırasında hataları işleme hakkında bilgi için bkz <xref:blazor/handle-errors#lifecycle-methods> ..

## <a name="stateful-reconnection-after-prerendering"></a>Prerendering sonrasında durum bilgisi olan yeniden bağlanma

Bir sunucu uygulamasında,, Blazor <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> bileşen başlangıçta sayfanın bir parçası olarak statik olarak işlenir. Tarayıcı sunucuya geri bir bağlantı kurduğunda, bileşen *yeniden*işlenir ve bileşen artık etkileşimli olur. Bileşeni başlatmak için [Onbaşlatılmış {Async}](#component-initialization-methods) yaşam döngüsü yöntemi varsa, yöntem *iki kez*yürütülür:

* Bileşen statik olarak önceden kullanılırken.
* Sunucu bağlantısı kurulduktan sonra.

Bu, bileşen son işlendiğinde Kullanıcı arabiriminde görünen verilerde fark edilebilir bir değişikliğe neden olabilir.

Bir sunucu uygulamasında çift işleme senaryosunu önlemek için Blazor :

* Prerendering sırasında durumu önbelleğe almak için kullanılabilecek bir tanımlayıcı geçirin ve uygulamayı yeniden başlattıktan sonra durumu alma.
* Bileşen durumunu kaydetmek için prerendering sırasında tanımlayıcıyı kullanın.
* Önbelleğe alınan durumu almak için prerendering öğesinden sonra tanımlayıcıyı kullanın.

Aşağıdaki kod, bir `WeatherForecastService` şablon tabanlı Blazor sunucu uygulamasında, Çift işlemeyi engelleyen güncelleştirilmiş bir güncelleme göstermektedir:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
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
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> bkz <xref:blazor/hosting-model-configuration#render-mode> ..

## <a name="detect-when-the-app-is-prerendering"></a>Uygulamanın ne zaman prerendering olduğunu Algıla

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>İptal edilebilen arka plan çalışması

Bileşenler genellikle ağ çağrıları yapma ( <xref:System.Net.Http.HttpClient> ) ve veritabanlarıyla etkileşim kurma gibi uzun süre çalışan arka plan işleri gerçekleştirir. Çeşitli durumlarda sistem kaynaklarını korumak için arka plan işinin durdurulması istenebilir. Örneğin, bir Kullanıcı bir bileşenden uzaklaştığında arka planda zaman uyumsuz işlemler otomatik olarak durdurulur.

Arka plan iş öğelerinin iptal etme gerektirmesinin diğer nedenleri şunlardır:

* Hatalı giriş verileriyle veya işleme parametreleriyle çalışan bir arka plan görevi başlatıldı.
* Çalışan arka plan iş öğelerinin geçerli kümesi, yeni bir iş öğesi kümesiyle değiştirilmelidir.
* Yürütülmekte olan görevlerin önceliği değiştirilmelidir.
* Uygulamanın sunucuya yeniden dağıtılması için kapatılması gerekebilir.
* Sunucu kaynakları sınırlı hale gelir, arka plan iş öğelerinin yeniden çizelgetasarımda.

Bir bileşene iptal edilebilen bir arka plan çalışma deseninin uygulanması için:

* Ve kullanın <xref:System.Threading.CancellationTokenSource> <xref:System.Threading.CancellationToken> .
* [Bileşenin elden çıkarılmasında](#component-disposal-with-idisposable) ve herhangi bir noktada iptal işlemi, belirteci el ile iptal ederek istenir [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A)
* Zaman uyumsuz çağrı geri döndüğünde, <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> belirteci çağırın.

Aşağıdaki örnekte:

* `await Task.Delay(5000, cts.Token);`uzun süreli zaman uyumsuz arka plan çalışmasını temsil eder.
* `BackgroundResourceMethod`yöntemi çağrılmadan önce atıldığı takdirde, uzun süre çalışan bir arka plan yöntemini temsil eder `Resource` .

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
