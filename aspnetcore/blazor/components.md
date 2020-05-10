---
title: ASP.NET Core Razor bileşenleri oluşturma ve kullanma
author: guardrex
description: Verileri bağlama, olayları işleme ve Razor bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: de8696488292dfc409c5ed83ea0b88a54a001696
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003064"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor bileşenleri oluşturma ve kullanma

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Blazoruygulamalar, *Bileşenler*kullanılarak oluşturulmuştur. Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir. Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir. Bileşenler esnek ve hafif. Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.

## <a name="component-classes"></a>Bileşen sınıfları

Bileşenler, C# ve [Razor](xref:mvc/views/razor) HTML biçimlendirme birleşimi kullanılarak bileşen dosyalarında (*. Razor*) uygulanır. İçindeki Blazor bir bileşen bir * Razor bileşen*olarak adlandırılır.

Bir bileşenin adı, büyük harfle başlamalıdır. Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.

Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı [Razor](xref:mvc/views/razor)gömülü C# sözdizimi kullanılarak eklenir. Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür. Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.

Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır. `@code` Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir. Birden çok `@code` blok izin verilir.

Bileşen üyeleri, ile `@`başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir. Örneğin, bir C# alanı alan adının önüne eklenerek `@` işlenir. Aşağıdaki örnek değerlendirilir ve işler:

* `headingFontStyle`için CSS özellik değerine `font-style`.
* `headingText``<h1>` öğenin içeriğine.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur. Blazorardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.

Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir. Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur. Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.

Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir. Uygulamanın kök ad alanı ise `BlazorApp` ve `Counter` bileşen *Sayfalar* klasöründe bulunuyorsa:

* `Counter` Bileşenin ad alanı `BlazorApp.Pages`.
* Bileşenin tam nitelikli tür adı `BlazorApp.Pages.Counter`.

Daha fazla bilgi için [bileşenleri Içeri aktarma](#import-components) bölümüne bakın.

Özel bir klasör kullanmak için, özel klasörün ad alanını üst bileşene ya da uygulamanın *_Imports. Razor* dosyasına ekleyin. Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı olduğunda bir *Bileşenler* klasöründeki bileşenleri kullanılabilir yapar `BlazorApp`:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statik varlıklar

BlazorProjenin [Web kökü (Wwwroot) klasörü](xref:fundamentals/index#web-root)altında statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.

Statik bir varlık için Web köküne başvurmak`/`üzere temel göreli bir yol () kullanın. Aşağıdaki örnekte, *logo. png* fiziksel olarak *{Project root}/Wwwroot/Images* klasöründe bulunur:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razorbileşenler, tilde işareti gösterimini (`~/` **) desteklemez.**

Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:host-and-deploy/blazor/index#app-base-path>..

## <a name="tag-helpers-arent-supported-in-components"></a>Etiket Yardımcıları bileşenlerde desteklenmiyor

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , Razor bileşenlerinde (*. Razor* dosyaları) desteklenmez. ' De Blazoretiket Yardımcısı benzeri işlevsellik sağlamak Için, etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.

## <a name="use-components"></a>Bileşenleri kullanma

Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir. Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.

*Index. Razor* dosyasında aşağıdaki biçimlendirme bir `HeadingComponent` örneği işler:

```razor
<HeadingComponent />
```

*Bileşenler/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır. Bileşenin ad `@using` alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.

## <a name="routing"></a>Yönlendirme

Uygulamasında Blazor yönlendirme, uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir.

Yönergeyle Razor bir dosya derlendiğinde, oluşturulan sınıfa yol şablonunu belirten bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> değer verilir. `@page` Çalışma zamanında, yönlendirici bileşen sınıflarını bir `RouteAttribute` ile arar ve hangi BILEŞENIN istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.

```razor
@page "/ParentComponent"

...
```

Daha fazla bilgi için bkz. <xref:blazor/routing>.

## <a name="parameters"></a>Parametreler

### <a name="route-parameters"></a>Rota parametreleri

Bileşenler, `@page` yönergede belirtilen yol şablonundan rota parametreleri alabilir. Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.

*Pages/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

İsteğe bağlı parametreler desteklenmez, bu nedenle `@page` önceki örnekte iki yönergeler uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci `@page` yönerge, `{text}` Route parametresini alır ve değeri `Text` özelliğine atar.

Birden çok klasör sınırlarındaki Razor yolu`*`/`**`yakalayan *catch-all* parametresi sözdizimi (), bileşenlerde (*. Razor* **) desteklenmez.**

### <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler, bileşen sınıfında `[Parameter]` özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir. Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

*Bileşenler/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Örnek uygulamadan aşağıdaki örnekte, öğesinin `ParentComponent` `Title` özelliğinin değerini ayarlar. `ChildComponent`

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Kendi *bileşen parametrelerine*yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın. Daha fazla bilgi için [kendi parametre özelliklerine yazan bileşenleri oluşturma](#dont-create-components-that-write-to-their-own-parameter-properties) bölümüne bakın.

## <a name="child-content"></a>Alt içerik

Bileşenler, başka bir bileşenin içeriğini ayarlayabilir. Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.

Aşağıdaki örnekte, öğesinin işlemek için `ChildComponent` bir Kullanıcı `ChildContent` arabirimi segmentini temsil `RenderFragment`eden öğesini temsil eden bir özelliği vardır. Değeri `ChildContent` , bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır. Değeri `ChildContent` , ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body`.

*Bileşenler/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` İçeriği alan özelliğin kural tarafından adlandırılması `ChildContent` gerekir.

Örnek `ParentComponent` uygulamadaki ' de içeriği `ChildComponent` `<ChildComponent>` etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz.

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Öznitelik döndürme ve rastgele parametreler

Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir. Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen [`@attributes`](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* . Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır. Örneğin, çok sayıda parametreyi destekleyen bir `<input>` için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.

Aşağıdaki örnekte `<input>` , ilk öğesi (`id="useIndividualParams"`) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe (`id="useAttributesDict"`) öznitelik splatesini kullanır:

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

Parametrenin türü dize anahtarlarıyla gerçekleştirmelidir `IEnumerable<KeyValuePair<string, object>>` . Bu `IReadOnlyDictionary<string, object>` senaryoda ayrıca bir seçenek de vardır.

Her iki `<input>` yaklaşımın de kullanıldığı işlenen öğeler aynıdır:

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

Rastgele öznitelikleri kabul etmek için `[Parameter]` `CaptureUnmatchedValues` özelliği olarak `true`ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Üzerindeki `CaptureUnmatchedValues` `[Parameter]` özelliği, parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar. Bir bileşen yalnızca ile `CaptureUnmatchedValues`tek bir parametre tanımlayabilir. İle `CaptureUnmatchedValues` kullanılan özellik türü dize anahtarlarıyla atanabilir `Dictionary<string, object>` olmalıdır. `IEnumerable<KeyValuePair<string, object>>`Ayrıca `IReadOnlyDictionary<string, object>` , Bu senaryodaki seçenekler de vardır.

Öğe özniteliklerinin konumuna `@attributes` göreli konumu önemlidir. Öğe `@attributes` üzerinde ne zaman bırakıldığında, öznitelikler sağdan sola (son-ilk) işlenir. Bir `Child` bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` Bileşenin `extra` özniteliği öğesinin `@attributes`sağına ayarlanır. Öznitelikler `Parent` sağdan sola ( `<div>` en `extra="5"` son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:

```html
<div extra="5" />
```

`extra` Aşağıdaki örnekte, `@attributes` ve sırası `Child` bileşen tarafından `<div>`tersine çevrilir:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Bileşendeki işlenen `<div>` ek öznitelik ile geçirildiğinde şunları içerir `extra="10"` `Parent`

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Bileşenlere başvuruları yakala

Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya `Show` `Reset`gibi komutları bu örneğe verebilirsiniz. Bir bileşen başvurusunu yakalamak için:

* Alt bileşene [`@ref`](xref:mvc/views/razor#ref) bir öznitelik ekleyin.
* Alt bileşenle aynı türde bir alan tanımlayın.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur. Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.

> [!IMPORTANT]
> `loginDialog` Değişken yalnızca bileşen işlendikten sonra ve çıktısı `MyLoginDialog` öğesi içerdiğinde doldurulur. Bu noktaya kadar başvurulmasına hiçbir şey yok. Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için [Onafterrenderasync veya OnAfterRender yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.

Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir. Bileşen başvuruları yalnızca .NET kodunda kullanıldıkları JavaScript&mdash;koduna aktarılmaz.

> [!NOTE]
> Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.** Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın. Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.

## <a name="invoke-component-methods-externally-to-update-state"></a>Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır

Blazoryürütmenin tek `SynchronizationContext` bir mantıksal iş parçacığını zorlamak için bir kullanır. Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/lifecycle) ve tarafından Blazor oluşturulan tüm olay geri çağırmaları bunun `SynchronizationContext`üzerinde yürütülür. Bir bileşenin bir zamanlayıcı veya diğer bildirimler gibi dış bir olaya göre güncellenmesi gerekir, ' a gönderilen `InvokeAsync` Blazoryöntemini kullanın. `SynchronizationContext`

Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:

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

Bir tekın `NotifierService` olarak kaydolun:

* Webassembly ' de Blazor hizmeti hizmetine kaydedin `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor Sunucusunda, hizmetini hizmetine kaydedin `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Bir bileşeni `NotifierService` güncelleştirmek için kullanın:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

`NotifierService` Yukarıdaki örnekte, `OnNotify` bileşen yöntemini Blazoröğesinin `SynchronizationContext`dışında çağırır. `InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Öğe \@ve bileşenlerin korunmasını denetlemek için anahtar kullanın

Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazorbu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir. Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.

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

`People` Koleksiyonun içeriği, ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir. Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı `Details` parametre değerleri almak için değişebilir. Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir. Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.

Eşleme işlemi, [`@key`](xref:mvc/views/razor#key) Directive özniteliğiyle denetlenebilir. `@key`, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:

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

`People` Koleksiyon değiştiğinde, yayılma algoritması örnekler ve `<DetailsEditor>` `person` örnekler arasındaki ilişkilendirmeyi korur:

* Bir `Person` , `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır. Diğer örnekler değişmeden bırakılır.
* Listedeki bir `Person` konuma eklenirse, ilgili konuma bir yeni `<DetailsEditor>` örnek eklenir. Diğer örnekler değişmeden bırakılır.
* Girişler `Person` yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.

Bazı senaryolarda, kullanımı rerendering karmaşıklığını `@key` en aza indirir ve odak konumu gibi Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.

> [!IMPORTANT]
> Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir. Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.

### <a name="when-to-use-key"></a>Anahtar ne zaman \@kullanılır?

Genellikle, bir liste işlendiğinde (örneğin `@key` , bir `@foreach` blokta) ve tanımlamak için uygun bir değer varsa, `@key`bu işlem kullanım açısından mantıklı olur.

Bir nesne değiştiğinde bir `@key` öğeyi veya Blazor bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Değişiklik `@currentPerson` olursa, `@key` öznitelik yönergesi tüm Blazor `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar. Değişiklik sırasında `@currentPerson` hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir.

### <a name="when-not-to-use-key"></a>\@Anahtar ne zaman kullanılmaz?

İle `@key`yayılma yaparken bir performans maliyeti vardır. Performans maliyeti büyük değildir, ancak yalnızca öğenin veya `@key` bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.

`@key` Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur. Kullanmanın `@key` tek avantajı model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.

### <a name="what-values-to-use-for-key"></a>Anahtar için \@kullanılacak değerler

Genellikle, için `@key`aşağıdaki değer türlerinden birini sağlamak mantıklı olur:

* Model nesne örnekleri (örneğin, `Person` önceki örnekte olduğu gibi). Bu, nesne başvurusu eşitliğine göre koruma sağlar.
* Benzersiz tanımlayıcılar (örneğin, veya `int` `string` `Guid`türündeki birincil anahtar değerleri).

Bu değerlerin çakışmayın için `@key` kullanıldığından emin olun. Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur. Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Kendi parametre özelliklerine yazan bileşenler oluşturmayın

Parametreleri aşağıdaki koşullarda üzerine yazılır:

* Bir `RenderFragment`alt bileşenin içeriği ile işlenir.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.

Üst bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> çağrıldığında ve alt bileşene yeni parametre değerleri sağlandığında parametreler sıfırlanır.

Aşağıdaki `Expander` bileşeni göz önünde bulundurun:

* Alt içeriği işler.
* Bileşen parametresiyle alt içeriğin gösterilmesini değiştirir.

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

Bileşen `Expander` , çağıraetkileyebilecek `StateHasChanged`bir üst bileşene eklenir:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Başlangıçta, `Expander` bileşenleri `Expanded` özellikleri bir kez değiştiğinde bağımsız olarak davranır. Alt bileşenler, durumlarını beklendiği gibi korur. `StateHasChanged` Üst öğede çağrıldığında, ilk alt bileşenin `Expanded` parametresi ilk değeri (`true`) olarak döndürülür. İkinci bileşende `Expander` hiçbir alt `Expanded` içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.

Önceki senaryodaki durumu korumak için `Expander` bileşen içindeki özel bir *alanı* kullanarak, onun geçiş durumunu koruyun.

Aşağıdaki `Expander` bileşen:

* Üst öğeden `Expanded` bileşen parametre değerini kabul eder.
* [OnInitialized olaydaki](xref:blazor/lifecycle#component-initialization-methods)bir *özel alana* (`expanded`) bileşen parametre değerini atar.
* İç geçiş durumunu korumak için özel alanını kullanır.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a>Kısmi sınıf desteği

Razorbileşenler kısmi sınıflar olarak oluşturulur. Razorbileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:

* C# kodu, tek bir dosyada [`@code`](xref:mvc/views/razor#code) HTML işaretlemesi ve Razor kodu olan bir blokta tanımlanmıştır. BlazorŞablonlar, Razor bu yaklaşımı kullanarak bileşenlerini tanımlar.
* C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.

Aşağıdaki örnek, bir `Counter` `@code` Blazor şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir. HTML Markup, Razor Code ve C# kodu aynı dosyada:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

`Counter` Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin. Bileşenler tarafından Razor kullanılan tipik ad alanları şunlardır:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Temel sınıf belirtin

[`@inherits`](xref:mvc/views/razor#inherits) Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir. Aşağıdaki örnek, bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel `BlazorRocksBase`sınıfı nasıl devralmasını gösterir. Taban sınıfın türevi olması gerekir `ComponentBase`.

*Pages/BlazorRocks. Razor*:

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

Öznitelikler, [`@attribute`](xref:mvc/views/razor#attribute) yönergeyle birlikte Razor bileşenlerde belirtilebilir. Aşağıdaki örnek, bileşen sınıfına `[Authorize]` özniteliğini uygular:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Bileşenleri içeri aktar

İle Razor yazılmış bir bileşenin ad alanı, tabanlıdır (öncelik sırasına göre):

* [`@namespace`](xref:mvc/views/razor#namespace)dosya (*. Razor*) biçimlendirmesinde atama (`@namespace BlazorSample.MyNamespace`). Razor
* `RootNamespace` Proje, proje dosyasında (`<RootNamespace>BlazorSample</RootNamespace>`).
* Proje dosyasının dosya adından (*. csproj*) ve proje kökünden bileşen yolundan alınan proje adı. Örneğin, çerçeve ad alanına `BlazorSample.Pages` *{Project root}/Pages/Index.Razor* (*BlazorSample. csproj*) çözümleniyor. Bileşenler C# ad bağlama kurallarını izler. Bu örnekteki `Index` bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:
  * Aynı klasörde, *sayfalarda*.
  * Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.

Farklı bir ad alanında tanımlanan bileşenler, ' ın Razor [`@using`](xref:mvc/views/razor#using) yönergesi kullanılarak kapsama alınır.

*BlazorSample/Shared/* klasöründe başka bir bileşen `Index.razor` `@using` `NavMenu.razor`varsa, bileşeni aşağıdaki ifadesiyle birlikte kullanılabilir:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`](xref:mvc/views/razor#using) yönergeyi gerektirmez:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` Nitelendirme desteklenmiyor.
>
> Diğer ad `using` deyimleri (örneğin, `@using Foo = Bar`) ile bileşenleri içeri aktarma desteklenmiyor.
>
> Kısmen nitelenmiş adlar desteklenmez. Örneğin, ile `@using BlazorSample` `<Shared.NavMenu></Shared.NavMenu>` ekleme ve başvuru `NavMenu.razor` desteklenmez.

## <a name="conditional-html-element-attributes"></a>Koşullu HTML öğesi öznitelikleri

HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir. Değer veya `false` `null`ise, öznitelik işlenmez. Değer ise `true`, öznitelik küçültülmüş olarak işlenir.

Aşağıdaki örnekte, `IsCompleted` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini `checked` belirler:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` İse `true`, onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" checked />
```

`IsCompleted` İse `false`, onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" />
```

Daha fazla bilgi için bkz. <xref:mvc/views/razor>.

> [!WARNING]
> .NET türü bir `bool`olduğunda, [Aria-BASıLAN](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)gibi bazı HTML öznitelikleri düzgün şekilde çalışmaz. Bu durumlarda, yerine bir `string` tür kullanın. `bool`

## <a name="raw-html"></a>Ham HTML

Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir. Ham HTML işlemek için, HTML içeriğini bir `MarkupString` değerde sarın. Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.

> [!WARNING]
> Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!

Aşağıdaki örnek, bir bileşenin işlenmiş `MarkupString` ÇıKTıSıNA statik HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Değerleri ve parametreleri basamaklama

Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir. Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir. Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.

### <a name="theme-example"></a>Tema örneği

Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.

*Uıthemeclasses/Themeınfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir. `CascadingValue` Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.

Örneğin, örnek uygulama, uygulamanın düzenleriyle,`ThemeInfo` `@Body` özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir. `ButtonClass`öğesinin `btn-success` bir değeri, düzen bileşeninde atanır. Tüm alt bileşenler bu özelliği `ThemeInfo` basamaklı nesne aracılığıyla kullanabilir.

`CascadingValuesParametersLayout`bileşeninde

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Basamaklı değerlerin kullanılması için, bileşenler `[CascadingParameter]` özniteliği kullanarak Geçişli Parametreler bildirir. Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.

Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar. Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.

`CascadingValuesParametersTheme`bileşeninde

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, her `Name` `CascadingValue` bileşene ve karşılık gelen `CascadingParameter`öğesine benzersiz bir dize sağlayın. Aşağıdaki örnekte, iki `CascadingValue` bileşen adına `MyCascadingType` göre farklı örneklerini basamakla:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:

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

Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır. Örneğin, örnek uygulamada aşağıdaki *Tabset* örneğini göz önünde bulundurun.

Örnek uygulamada, sekmelerin uygulandığı `ITab` bir arabirim vardır:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Bileşen, çeşitli `Tab` bileşenleri `TabSet` içeren bileşenini kullanır: `CascadingValuesParametersTabSet`

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

Alt `Tab` bileşenler, `TabSet`öğesine açıkça parametre olarak aktarılmaz. Bunun yerine, alt `Tab` bileşenleri öğesinin alt içeriğinin bir parçasıdır `TabSet`. Bununla birlikte, `TabSet` üst bilgileri ve etkin sekmeyi işleyebilmesi için her `Tab` bileşen hakkında hala bilmeniz gerekir. Ek kod gerektirmeden bu koordinasyonu etkinleştirmek için `TabSet` bileşen, kendisini alt `Tab` bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* .

`TabSet`bileşeninde

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Alt `Tab` bileşenler, kapsayan `TabSet` ' i basamaklı bir parametre olarak yakalar, bu `Tab` nedenle bileşenler, bu sekmenin `TabSet` etkin olduğu ve koordinasyonu üzerine eklenir.

`Tab`bileşeninde

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razorşablondan

İşleme parçaları, şablon sözdizimi kullanılarak Razor tanımlanabilir. RazorŞablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Aşağıdaki örnek, bir bileşeni doğrudan bir `RenderFragment` bileşende `RenderFragment<T>` nasıl belirtdiğini ve işleneceğini gösterir. Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/templated-components)bağımsız değişken olarak da geçirilebilir.

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Önceki kodun işlenmiş çıktısı:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Ölçeklenebilir vektör grafik (SVG) görüntüleri

Bu Blazor yana, ölçeklenebilir vektör GRAFIKLERI (SVG) görüntüleri (*. SVG*) dahil olmak üzere HTML, tarayıcı tarafından desteklenen görüntüler `<img>` etiketi aracılığıyla desteklenir:

```html
<img alt="Example image" src="some-image.svg" />
```

Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının (*. css*) CSS kurallarında desteklenir:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez. Bir `<svg>` etiketi doğrudan bir bileşen dosyasına (*. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir. Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve `@bind` bazı SVG etiketleriyle kullanılamaz. Gelecekteki bir sürümde bu sınırlamaları ele almayı bekliyoruz.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/server/threat-mitigation>&ndash; Kaynak tükenmesi ile Çekişmek zorunda olması gereken sunucu uygulamaları oluşturmaya Blazor yönelik yönergeler içerir.
