---
title: Core Razor bileşenleri ASP.NET oluşturma ve kullanma
author: guardrex
description: Verilere nasıl bağlanılacağı, olayları nasıl işleyeceğiniz ve bileşen yaşam döngülerini nasıl yönetleyeceğiniz de dahil olmak üzere Razor bileşenlerini nasıl oluşturup kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791483"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Core Razor bileşenleri ASP.NET oluşturma ve kullanma

Yazar: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), ve [Tobias Bartsch](https://www.aveo-solutions.com/)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Blazoruygulamalar *bileşenleri*kullanılarak oluşturulur. Bileşen, sayfa, iletişim kutusu veya form gibi bağımsız bir kullanıcı arabirimi (UI) bileşenidir. Bileşen, HTML biçimlendirmesini ve veri enjekte etmek veya UI olaylarına yanıt vermek için gereken işleme mantığını içerir. Bileşenler esnek ve hafiftir. Bunlar iç içe, yeniden kullanılabilir ve projeler arasında paylaşılabilir.

## <a name="component-classes"></a>Bileşen sınıfları

Bileşenler, C# ve HTML biçimlendirmesinin bir kombinasyonu kullanılarak [Razor](xref:mvc/views/razor) bileşen dosyalarında *(.jilet)* uygulanır. Bir bileşen Blazor resmen bir Razor *bileşeni*olarak adlandırılır.

Bir bileşenin adı büyük harfli bir karakterle başlamalıdır. Örneğin, *MyCoolComponent.razor* geçerlidir ve *myCoolComponent.razor* geçersizdir.

Bir bileşenin ui'si HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullular, ifadeler) [Razor](xref:mvc/views/razor)adlı katıştırılmış c# sözdizimi kullanılarak eklenir. Bir uygulama derlendiğinde, HTML biçimlendirmeve C# oluşturma mantığı bileşen sınıfına dönüştürülür. Oluşturulan sınıfın adı dosyanın adıyla eşleşir.

Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır. Blokta, `@code` bileşen durumu (özellikler, alanlar) olay işleme veya diğer bileşen mantığını tanımlamak için yöntemlerle belirtilir. Birden `@code` fazla blokca izin verilir.

Bileşen üyeleri, `@`'ile başlayan C# ifadeleri kullanılarak bileşenin oluşturma mantığının bir parçası olarak kullanılabilir. Örneğin, bir C# alanı alan adına `@` öne doğru edilerek işlenir. Aşağıdaki örnek, şunları değerlendirir ve işler:

* `_headingFontStyle`için CSS özellik `font-style`değerine.
* `_headingText`öğenin içeriğine. `<h1>`

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Bileşen başlangıçta işlendikten sonra, bileşen olaylara yanıt olarak işağacını yeniden oluşturur. Blazorsonra yeni işleme ağacını öncekiyle karşılaştırır ve tarayıcının Belge Nesnesi Modeli'nde (DOM) herhangi bir değişiklik uygular.

Bileşenler sıradan C# sınıflarıdır ve proje içinde herhangi bir yere yerleştirilebilir. Web sayfaları üreten bileşenler genellikle *Sayfalar* klasöründe bulunur. Sayfa dışı bileşenler sık sık *Paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.

Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve bileşenin uygulama içindeki konumundan (klasör) türetilir. Uygulamanın kök ad alanı `BlazorApp` ise `Counter` ve bileşen *Sayfalar* klasöründe bulunursa:

* Bileşenin `Counter` ad `BlazorApp.Pages`alanı.
* Bileşenin tam nitelikli tür `BlazorApp.Pages.Counter`adı.

Daha fazla bilgi [için, İçe Aktar bileşenleri](#import-components) bölümüne bakın.

Özel bir klasör kullanmak için, özel klasörün ad alanını ana bileşene veya uygulamanın *_Imports.razor* dosyasına ekleyin. Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı `BlazorApp`olduğunda *Bileşenler* klasöründeki bileşenleri kullanılabilir hale getirir:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statik varlıklar

Blazorprojenin [web kökü (wwwroot) klasörüne](xref:fundamentals/index#web-root)statik varlıkları yerleştiren ASP.NET Core uygulamaları kuralını izler.

Statik bir varlık için`/`web köküne başvurmak için temel göreli bir yol ( ) kullanın. Aşağıdaki örnekte, *logo.png* fiziksel olarak *{PROJECT ROOT}/wwwroot/images* klasöründe yer almaktadır:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Jilet bileşenleri tilde-slash gösterimi`~/` **desteklemez** ( ).

Bir uygulamanın temel yolunu ayarlama hakkında <xref:host-and-deploy/blazor/index#app-base-path>bilgi için bkz.

## <a name="tag-helpers-arent-supported-in-components"></a>Tag Yardımcıları bileşenlerde desteklenmez

[Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) Razor bileşenlerinde *(.jilet* dosyaları) desteklenmez. Tag Helper benzeri işlevselliği Blazorsağlamak için, Tag Helper ile aynı işlevsellik teshisine sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.

## <a name="use-components"></a>Bileşenleri kullanma

Bileşenler, HTML öğesi sözdizimini kullanarak bunları beyan ederek diğer bileşenleri içerebilir. Bir bileşeni kullanmanın biçimlendirmesi, etiketin adının bileşen türü olduğu bir HTML etiketine benzer.

*Index.razor'daki* aşağıdaki biçimlendirme `HeadingComponent` bir örneği işler:

```razor
<HeadingComponent />
```

*Bileşenler/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Bir bileşen, bir bileşen adı eşleşmeyen bir büyük harfe sahip bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu belirten bir uyarı yayımlanır. Bileşenin `@using` ad alanı için bir yönerge eklemek bileşeni kullanılabilir hale getirir ve bu da uyarıyı çözer.

## <a name="routing"></a>Yönlendirme

Yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlayarak elde edilir.

Yönergesi olan `@page` bir Razor dosyası derlendiğinde, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> oluşturulan sınıfa rota şablonu belirten bir verilir. Çalışma zamanında, yönlendirici a `RouteAttribute` ile bileşen sınıfları arar ve hangi bileşen istenen URL eşleşen bir rota şablonu vardır işler.

```razor
@page "/ParentComponent"

...
```

Daha fazla bilgi için bkz. <xref:blazor/routing>.

## <a name="parameters"></a>Parametreler

### <a name="route-parameters"></a>Rota parametreleri

Bileşenler, yönergede sağlanan rota şablonundan rota parametrelerini `@page` alabilir. Yönlendirici, ilgili bileşen parametrelerini doldurmak için rota parametrelerini kullanır.

*Sayfalar/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

İsteğe bağlı parametreler desteklenmez, bu nedenle önceki örnekte iki `@page` yönerge uygulanır. İlk bir parametre olmadan bileşene navigasyon izin verir. İkinci `@page` yönerge `{text}` rota parametresini alır ve `Text` değeri özelliğe atar.

Birden çok klasör sınırları`*`/`**`boyunca yolu yakalayan *catch-all* parametre sözdizimi (*.razor)* ile **desteklenmez.**

### <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler, öznitelik ile bileşen sınıfında ortak özellikleri kullanılarak `[Parameter]` tanımlanan *bileşen parametreleri*olabilir. Biçimlendirmede bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

*Bileşenler/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` `ChildComponent`'nin özelliğinin değerini ayarlar.

*Sayfalar/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Kendi bileşen parametrelerine yazan *bileşenler*oluşturmayın, bunun yerine özel bir alan kullanın. Daha fazla bilgi için, [kendi parametre özellikleri bölümüne yazan bileşenler oluşturmabölümüne](#dont-create-components-that-write-to-their-own-parameter-properties) bakın.

## <a name="child-content"></a>Alt içerik

Bileşenler başka bir bileşenin içeriğini ayarlayabilir. Atama bileşeni, alıcı bileşeni belirten etiketler arasında içerik sağlar.

Aşağıdaki örnekte, `ChildComponent` işlenebilmek için `RenderFragment`UI'nin bir kesimini temsil eden bir `ChildContent` özelliği vardır. `ChildContent` Değer, içeriğin işlenmesi gereken bileşenin biçimlendirmesinde konumlandırılır. Değeri `ChildContent` üst bileşenden alınır ve Bootstrap panelin içinde `panel-body`işlenir.

*Bileşenler/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> İçeriği alan özelliğin `RenderFragment` adı `ChildContent` sözleşmeyle adlandırılmalıdır.

Örnek `ParentComponent` uygulamadaki içerik `ChildComponent` `<ChildComponent>` etiketlerin içine yerleştirerek işlemek için içerik sağlayabilir.

*Sayfalar/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Öznitelik ve rasgele parametreler

Bileşenler, bileşenin bildirilen parametrelerine ek olarak ek öznitelikleri yakalayabilir ve işleyebilir. Ek öznitelikler bir sözlükte yakalanabilir ve bileşen [`@attributes`](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir öğeye *splatted.* Bu senaryo, çeşitli özelleştirmeleri destekleyen bir biçimlendirme öğesi üreten bir bileşeni tanımlarken yararlıdır. Örneğin, birçok parametreyi destekleyen bir öznitelikleri `<input>` ayrı ayrı tanımlamak sıkıcı olabilir.

Aşağıdaki örnekte, birinci `<input>` öğe`id="useIndividualParams"`( ) tek tek `<input>` bileşen`id="useAttributesDict"`parametrelerini kullanırken, ikinci öğe ( ) öznitelik splatting kullanır:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Parametre türü dize `IEnumerable<KeyValuePair<string, object>>` tuşları ile uygulanmalıdır. Kullanmak `IReadOnlyDictionary<string, object>` da bu senaryoda bir seçenektir.

Her iki `<input>` yaklaşımı kullanarak işlenen öğeler aynıdır:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Rasgele öznitelikleri kabul etmek için, `[Parameter]` `CaptureUnmatchedValues` `true`özellik kümesi ile öznitelik kullanarak bir bileşen parametresi tanımlayın:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Üzerindeki `CaptureUnmatchedValues` `[Parameter]` özellik, parametrenin diğer parametreyle eşleşmeyecek tüm öznitelikleri eşleştirmesine olanak tanır. Bir bileşen yalnızca `CaptureUnmatchedValues`. Kullanılan `CaptureUnmatchedValues` özellik türü dize tuşları `Dictionary<string, object>` ile atanabilir olmalıdır. `IEnumerable<KeyValuePair<string, object>>`veya `IReadOnlyDictionary<string, object>` bu senaryoda seçenekler de vardır.

Öğe özniteliklerinin `@attributes` konumuna göre konum önemlidir. Öğeye splatted zaman, `@attributes` öznitelikleri sağdan sola (sondan birinciye) işlenir. Bir `Child` bileşeni tüketen bir bileşenin aşağıdaki örneğini göz önünde bulundurun:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Bileşenin `Child` özniteliği' nin `extra` sağında `@attributes`ayarlanır. Öznitelikler `Parent` sağdan `<div>` sola (sondan birinciye) işlendiğinden, bileşenin işlenmesi ek öznitelikten `extra="5"` geçirildiğinde içerir:

```html
<div extra="5" />
```

`extra` Aşağıdaki örnekte, `@attributes` `Child` bileşenin `<div>`sırası ve tersine çevrilmesi:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Ek öznitelik geçirildiğinde `extra="10"` bileşende işlenen `<div>` içerir: `Parent`

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Bileşenlere yapılan başvuruları yakalama

Bileşen başvuruları, söz konusu örne komutlar verebilmeniz için bileşen `Show` örneğine başvurmanın bir yolunu `Reset`sağlar. Bileşen başvurularını yakalamak için:

* Alt [`@ref`](xref:mvc/views/razor#ref) bileşene bir öznitelik ekleyin.
* Alt bileşenle aynı türde bir alan tanımlayın.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Bileşen işlendiğinde, `_loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur. Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.

> [!IMPORTANT]
> `_loginDialog` Değişken yalnızca bileşen işlendikten sonra doldurulur ve `MyLoginDialog` çıktısı öğeyi içerir. O ana kadar atıfta bulunulacak bir şey yok. Bileşen işleme bittikten sonra bileşen başvurularını işlemek için [OnAfterRenderAsync veya OnAfterRender yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.

Bir döngüdeki bileşenlere başvurmak [#13358 için](https://github.com/dotnet/aspnetcore/issues/13358)bkz.

Bileşen başvurularını yakalama öğesi başvuruları [yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanırken, bu bir JavaScript interop özelliği değildir. Bileşen başvuruları yalnızca .NET kodunda kullanılan JavaScript koduna&mdash;geçirilir.

> [!NOTE]
> Alt **not** bileşenlerin durumunu mutasyona uğratmak için bileşen referansları kullanmayın. Bunun yerine, alt bileşenlere veri aktarmak için normal bildirim parametrelerini kullanın. Normal bildirim parametrelerinin kullanılması, doğru zamanlarda otomatik olarak yeniden işlenen alt bileşenlerle sonuçlanır.

## <a name="invoke-component-methods-externally-to-update-state"></a>Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağırma

Blazoryürütme `SynchronizationContext` tek bir mantıksal iş parçacığı zorlamak için a kullanır. Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/lifecycle) ve tarafından Blazor yükseltilen tüm `SynchronizationContext`olay geri aramaları bu şekilde yürütülür. Bir bileşenin zamanlayıcı veya diğer bildirimler gibi harici bir olaya göre güncellenmesi `InvokeAsync` gerektiğinde, Blazor''nin `SynchronizationContext`''sine ' gönderilecek yöntemi kullanın.

Örneğin, güncelleştirilmiş durum herhangi bir dinleme bileşeni bildirebilir bir *noter hizmeti* düşünün:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Tekli `NotifierService` olarak kaydedin:

* WebAssembly'de, Blazor hizmeti `Program.Main`şu şekilde kaydettirin:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Sunucu'da, Blazor hizmeti `Startup.ConfigureServices`şu şekilde kaydettirin:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Bir `NotifierService` bileşeni güncelleştirmek için kullanın:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

`NotifierService` Önceki örnekte, `OnNotify` bileşenin yöntemi Blazor'nin `SynchronizationContext`dışında çağırır. `InvokeAsync`doğru içeriğe geçmek ve bir işleme sıraya girmek için kullanılır.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Elementlerin ve bileşenlerin korunmasını kontrol etmek için anahtarı kullanın \@

Öğelerin veya bileşenlerin ve öğelerin veya bileşenlerin Blazorbir listesini işlerken, 'ın disme algoritması önceki öğelerden veya bileşenlerden hangilerinin tutulabildiğine ve model nesnelerinin bunlara nasıl eşleneceklerine karar vermelidir. Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.

Aşağıdaki örneği inceleyin:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Koleksiyonun `People` içeriği eklenen, silinen veya yeniden sipariş edilen girişlerle değişebilir. Bileşen yeniden işlendiğinde, `<DetailsEditor>` bileşen farklı `Details` parametre değerleri alacak şekilde değişebilir. Bu beklenenden daha karmaşık yeniden işleme neden olabilir. Bazı durumlarda, yeniden oluşturma, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.

Eşleme [`@key`](xref:mvc/views/razor#key) işlemi, yönerge özniteliği ile denetlenebilir. `@key`difüzyon algoritmasının anahtarın değerine göre öğelerin veya bileşenlerin korunmasını garanti etmesi neden olur:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Koleksiyon değiştiğinde, difüzör aletme `<DetailsEditor>` algoritması `person` örnekler ve örnekler arasındaki ilişkiyi korur: `People`

* Listeden `Person` `People` a silinirse, ui'den yalnızca karşılık gelen `<DetailsEditor>` örnek kaldırılır. Diğer örnekler değişmeden bırakılır.
* Listedeki `Person` bazı konuma a eklenirse, bu `<DetailsEditor>` ilgili konuma yeni bir örnek eklenir. Diğer örnekler değişmeden bırakılır.
* `Person` Girişler yeniden sıralanırsa, ilgili `<DetailsEditor>` örnekler korunur ve UI'de yeniden sıralanır.

Bazı senaryolarda, yeniden `@key` işlemenin karmaşıklığını en aza indirir ve odak konumu gibi DOM'un durum lu bölümleriyle ilgili olası sorunları önler.

> [!IMPORTANT]
> Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir. Anahtarlar belge genelinde genel olarak karşılaştırılmıyor.

### <a name="when-to-use-key"></a>Anahtar ne \@zaman kullanılır?

Tipik olarak, bir liste `@key` işlendiğinde (örneğin, bir `@foreach` blokta) ve uygun bir değer tanımlamak `@key`için var olduğunda kullanmak mantıklı.

Bir nesne `@key` değiştiğinde Blazor bir öğeyi veya bileşen alt ağacını korumayı önlemek için de kullanabilirsiniz:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Değişiklik `@currentPerson` olursa, `@key` öznitelik Blazor yönergesi `<div>` tüm ünü ve soyundan gelenleri atmaya ve ui içindeki alt ağacı yeni öğeler ve bileşenlerle yeniden oluşturmaya zorlar. Bu, değişiklik olduğunda `@currentPerson` hiçbir UI durumunun korunduğunu garanti etmek gerekirse yararlı olabilir.

### <a name="when-not-to-use-key"></a>Anahtar kullanılmadığında \@

'ile difüzör yaparken `@key`bir performans maliyeti vardır. Performans maliyeti büyük değildir, ancak `@key` yalnızca öğeyi veya bileşen koruma kurallarının denetlenerek uygulamaya yarar sağolup olmadığını belirtin.

`@key` Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur. Kullanmanın `@key` tek avantajı, eşlemeyi seçen difüzör algoritması yerine, model örneklerinin korunan bileşen örneklerine *nasıl* eşlendirileceği üzerinde denetim yapmaktır.

### <a name="what-values-to-use-for-key"></a>Anahtar için \@kullanılacak değerler

Genel olarak, aşağıdaki değer türlerinden birini sağlamak `@key`mantıklıdır:

* Örnek nesne örnekleri (örneğin, `Person` önceki örnekte olduğu gibi bir örnek). Bu, nesne başvuru eşitliğine dayalı koruma sağlar.
* Benzersiz tanımlayıcılar (örneğin, tür `int`, `string`veya `Guid`birincil anahtar değerleri).

Kullanılan `@key` değerlerin çakışmadığından emin olun. Çakışan değerler aynı üst öğe Blazor içinde algılanırsa, eski öğeleri veya bileşenleri yeni öğelerveya bileşenlerle deterministically eşleyemediği için bir özel durum oluşturur. Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerler kullanın.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Kendi parametre özelliklerine yazan bileşenler oluşturmayın

Parametreler aşağıdaki koşullar altında üzerine yazılır:

* Bir alt bileşenin içeriği bir `RenderFragment`.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.

Ana bileşen çağrıldığında <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> yeniden işlendiği ve alt bileşene yeni parametre değerleri sağlandığı için parametreler sıfırlanır.

Aşağıdaki `Expander` bileşeni göz önünde bulundurun:

* Alt içerik işler.
* Alt içeriği bileşen parametresi ile gösteren geçişler.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Bileşen, `Expander` şu leri çağırabilecek `StateHasChanged`bir üst bileşene eklenir:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Başlangıçta, `Expander` bileşenleri `Expanded` özellikleri değiştirildiğinde bağımsız olarak olur. Alt bileşenler durumlarını beklendiği gibi korur. Üst `StateHasChanged` öğe çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değerine`true`geri sıfırlanır ( ). İkinci `Expander` bileşende `Expanded` alt içerik işlenmediği için ikinci bileşenin değeri sıfırlanmıyor.

Önceki senaryoda durumu korumak için, *private field* değiştirilen `Expander` durumunu korumak için bileşendeki özel bir alanı kullanın.

Aşağıdaki `Expander` bileşen:

* `Expanded` Üst bileşen parametre değerini kabul eder.
* [OnInitialized olayında](xref:blazor/lifecycle#component-initialization-methods)bileşen parametre`_expanded`değerini özel bir *alana* ( ) atar.
* Dahili geçiş durumunu korumak için özel alanı kullanır.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a>Kısmi sınıf desteği

Jilet bileşenleri kısmi sınıflar olarak oluşturulur. Jilet bileşenleri aşağıdaki yaklaşımlardan biri kullanılarak yazılır:

* C# kodu, tek [`@code`](xref:mvc/views/razor#code) bir dosyada HTML biçimlendirmesi ve Razor kodu içeren bir blokta tanımlanır. Blazorşablonlar bu yaklaşımı kullanarak Razor bileşenlerini tanımlar.
* C# kodu kısmi sınıf olarak tanımlanan kod arkası dosyasına yerleştirilir.

Aşağıdaki örnekte, `Counter` Blazor şablondan `@code` oluşturulan bir uygulamada blok bulunan varsayılan bileşen gösterilmektedir. HTML biçimlendirmesi, Razor kodu ve C# kodu aynı dosyadadır:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

Bileşen, `Counter` kısmi sınıfa sahip bir kod arkası dosya kullanılarak da oluşturulabilir:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

Gerekli olan tüm ad alanlarını gerektiği gibi kısmi sınıf dosyasına ekleyin. Razor bileşenleri tarafından kullanılan tipik ad alanları şunlardır:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Taban sınıf belirtin

Yönerge, [`@inherits`](xref:mvc/views/razor#inherits) bir bileşen için taban sınıf belirtmek için kullanılabilir. Aşağıdaki örnek, bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir taban sınıfı `BlazorRocksBase`nasıl devralabileceğini gösterir. Taban sınıf' tan `ComponentBase`türemelisiniz.

*Sayfalar/BlazorRocks.jilet*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Bir öznitelik belirtin

Öznitelikler, yönerge ile [`@attribute`](xref:mvc/views/razor#attribute) Razor bileşenlerinde belirtilebilir. Aşağıdaki örnek bileşen `[Authorize]` sınıfına öznitelik uygular:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Bileşenleri içe aktarma

Razor ile yazılmış bir bileşenin ad alanı temel alınarak (öncelik sırasına göre):

* [`@namespace`](xref:mvc/views/razor#namespace)(*.razor*) biçimlendirme`@namespace BlazorSample.MyNamespace`( ).
* Proje proje `RootNamespace` dosyasında (`<RootNamespace>BlazorSample</RootNamespace>`).
* Proje dosyasının dosya adından *(.csproj)* ve proje kökünden bileşene giden yol alınan proje adı. Örneğin, çerçeve *{PROJECT ROOT}/Pages/Index.razor* *(BlazorSample.csproj)* ad alanına `BlazorSample.Pages`giderir. Bileşenler C# adı bağlama kurallarına uyar. Bu `Index` örnekteki bileşen için, kapsamdaki bileşenlerin tümü bileşenlerişunlardır:
  * Aynı klasörde, *Sayfalar*.
  * Projenin kökünde, açıkça farklı bir ad alanı belirtmeden bileşenler.

Farklı bir ad alanında tanımlanan bileşenler Razor [`@using`](xref:mvc/views/razor#using) yönergesi kullanılarak kapsam içine getirilir.

*BlazorSample/Shared/* klasöründe başka bir bileşen `Index.razor` `@using` `NavMenu.razor`varsa, bileşen aşağıdaki ifadeyle kullanılabilir:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Bileşenler, [`@using`](xref:mvc/views/razor#using) yönerge gerektirmeyen tam nitelikli adlarını kullanarak da başvurulabilir:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Yeterlilik `global::` desteklenmiyor.
>
> Diğer adlı `using` deyimlerle bileşenleri alma `@using Foo = Bar`(örneğin,) desteklenmez.
>
> Kısmen nitelikli adlar desteklenmez. Örneğin, ekleme `@using BlazorSample` ve başvurma `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.

## <a name="conditional-html-element-attributes"></a>Koşullu HTML öğesi öznitelikleri

HTML öğesi öznitelikleri .NET değerine göre koşullu olarak işlenir. Değer `false` veya, `null`öznitelik işlenmez. Değer ise, `true`öznitelik en aza indirgenmiş hale getirilir.

Aşağıdaki örnekte, `IsCompleted` öğenin `checked` biçimlendirmesinde işlenmeyi belirler:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`true`Ise, `IsCompleted` onay kutusu aşağıdaki gibi işlenir:

```html
<input type="checkbox" checked />
```

`false`Ise, `IsCompleted` onay kutusu aşağıdaki gibi işlenir:

```html
<input type="checkbox" />
```

Daha fazla bilgi için bkz. <xref:mvc/views/razor>.

> [!WARNING]
> [Arya basıldığında](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)ki bazı HTML öznitelikleri ,.NET türü . `bool` Bu gibi durumlarda, `string` `bool`bir . yerine bir tür kullanın

## <a name="raw-html"></a>Ham HTML

Dizeleri normalde DOM metin düğümleri kullanılarak işlenir, bu da içerebilecekleri herhangi bir biçimlendirmenin göz ardı edildiği ve gerçek metin olarak kabul edildiği anlamına gelir. Ham HTML işlemek için HTML içeriğini `MarkupString` bir değere sarın. Değer HTML veya SVG olarak ayrıştırılır ve DOM'a eklenir.

> [!WARNING]
> Herhangi bir güvenilmeyen kaynaktan oluşturulmuş ham HTML oluşturma bir **güvenlik riskidir** ve kaçınılmalıdır!

Aşağıdaki örnek, bir `MarkupString` bileşenin işlenen çıktısına statik HTML içeriği bloğu eklemek için türü kullanır:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Basamaklı değerler ve parametreler

Bazı senaryolarda, bileşen [parametrelerini](#component-parameters)kullanarak bir ata bileşeninden soyundan gelen bir bileşene veri akışı sakıncalıdır , özellikle de birkaç bileşen katmanı varsa. Basamaklı değerler ve parametreler, bir ata bileşeninin tüm soyundan geçen bileşenlere değer sağlaması için uygun bir yol sağlayarak bu sorunu çözer. Basamaklı değerler ve parametreler de bileşenlerin koordine etmesi için bir yaklaşım sağlar.

### <a name="theme-example"></a>Tema örneği

Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıf, bileşen hiyerarşisinden akacak tema bilgilerini, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaşmasını sağlar.

*UIThemeClasses/ ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Bir ata bileşeni Basamaklı Değer bileşenini kullanarak basamaklı bir değer sağlayabilir. Bileşen, `CascadingValue` bileşen hiyerarşisinin bir alt ağacını sarar ve bu alt ağaçtaki tüm bileşenlere tek bir değer sağlar.

Örneğin, örnek uygulama,`ThemeInfo` `@Body` uygulamanın düzenlerinden birinde tema bilgilerini , özelliğin düzen gövdesini oluşturan tüm bileşenler için basamaklı bir parametre olarak belirtir. `ButtonClass`düzen `btn-success` bileşeninde bir değer atanır. Herhangi bir azalan bileşen basamaklı nesne `ThemeInfo` aracılığıyla bu özelliği tüketebilir.

`CascadingValuesParametersLayout`Bileşen:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Basamaklı değerlerden yararlanmak için bileşenler, özniteliği kullanarak `[CascadingParameter]` basamaklı parametreleri bildirir. Basamaklı değerler, türe göre basamaklı parametrelere bağlıdır.

Örnek uygulamada, `CascadingValuesParametersTheme` bileşen basamaklı değeri `ThemeInfo` basamaklı bir parametreye bağlar. Parametre, bileşen tarafından görüntülenen düğmelerden biri için CSS sınıfını ayarlamak için kullanılır.

`CascadingValuesParametersTheme`Bileşen:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Aynı alt ağaç içinde aynı türde birden çok `Name` değer basamaklı `CascadingValue` için, `CascadingParameter`her bileşen ve karşılık gelen benzersiz bir dize sağlayın. Aşağıdaki örnekte, `CascadingValue` iki bileşen ada `MyCascadingType` göre farklı örnekleri basamaklı:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Bir soyundan gelen bileşende, basamaklı parametreler değerlerini ata bileşenindeki ilgili basamaklı değerlerden ada göre alır:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet örneği

Basamaklı parametreler, bileşenlerin bileşen hiyerarşisi içinde işbirliği yapmasına da olanak tanır. Örneğin, örnek uygulamada aşağıdaki *TabSet* örneğini göz önünde bulundurun.

Örnek uygulamaseks `ITab` uygulamak bir arayüze sahiptir:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Bileşen, `CascadingValuesParametersTabSet` birkaç `TabSet` `Tab` bileşen içeren bileşeni kullanır:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Alt `Tab` bileşenler açıkça parametre olarak `TabSet`geçirilir. Bunun yerine, `Tab` alt bileşenler alt içeriğinin `TabSet`bir parçasıdır. Ancak, `TabSet` üstbilgi ve etkin `Tab` sekmeyi işleyebilir, böylece yine de her bileşen hakkında bilmek gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek `TabSet` için, bileşen kendisini daha `Tab` sonra soyundan gelen bileşenler tarafından alınan basamaklı bir değer olarak *sağlayabilir.*

`TabSet`Bileşen:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Soyundan gelen `Tab` bileşenler, `TabSet` içereni basamaklı bir parametre `Tab` olarak yakalar, `TabSet` böylece bileşenler kendilerini sekmenin etkin olduğu koordinata ekler ve koordine eder.

`Tab`Bileşen:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Jilet şablonları

Render parçaları Razor şablonsözdizimi kullanılarak tanımlanabilir. Jilet şablonları, bir UI snippet tanımlamanın ve aşağıdaki biçimi varsaymanın bir yoludur:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Aşağıdaki örnekte, şablonların `RenderFragment` `RenderFragment<T>` doğrudan bir bileşende nasıl belirtilen ve değerlere verilebildiğini ve oluşturulacak şekilde gösterildiği gösterilebilir. Render parçaları da [şablonbileşenlerine](xref:blazor/templated-components)bağımsız değişken olarak geçirilebilir.

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Önceki kodun işlenme çıktısı:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Ölçeklenebilir Vektör Grafikleri (SVG) görüntüleri

HTML'yi Blazor işlediğinden, Ölçeklenebilir Vektör Grafikleri (SVG) görüntüleri *(.svg)* dahil olmak `<img>` üzere tarayıcı destekli görüntüler etiket üzerinden desteklenir:

```html
<img alt="Example image" src="some-image.svg" />
```

Benzer şekilde, SVG görüntüleri bir stylesheet dosyasının CSS kurallarında desteklenir (*.css):*

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Ancak, satır satırlı SVG biçimlendirmesi tüm senaryolarda desteklenmez. Bir `<svg>` etiketi doğrudan bileşen dosyasına *(.jilet)* yerleştirirseniz, temel görüntü oluşturma desteklenir, ancak birçok gelişmiş senaryo henüz desteklenmez. Örneğin, `<use>` etiketlere şu anda saygı `@bind` duyulmuyor ve bazı SVG etiketleri ile kullanılamaz. Bu sınırlamaları ileride piyasaya sürülecek şekilde ele almayı bekliyoruz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/server>&ndash; Kaynak tükenmesi ile uğraşmak gerekir Sunucu uygulamaları oluşturma Blazor kılavuzu içerir.
