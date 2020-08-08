---
title: ASP.NET Core bileşenleri oluşturma ve kullanma Razor
author: guardrex
description: RazorVerileri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil olmak üzere bileşenleri oluşturma ve kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: a145cfd551650445f9ff35259cbedf71ebb686f0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014600"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>ASP.NET Core bileşenleri oluşturma ve kullanma Razor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [tosapma Bartsch](https://www.aveo-solutions.com/) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Blazoruygulamalar, *Bileşenler*kullanılarak oluşturulmuştur. Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir. Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir. Bileşenler esnek ve hafif. Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.

## <a name="component-classes"></a>Bileşen sınıfları

Bileşenler, [Razor](xref:mvc/views/razor) `.razor` C# ve HTML biçimlendirmesinin bir birleşimi kullanılarak bileşen dosyalarında () uygulanır. İçindeki bir bileşen Blazor bir * Razor bileşen*olarak adlandırılır.

### <a name="no-locrazor-syntax"></a>Razorsözdizimi

Razoruygulamalardaki bileşenler Blazor yaygın olarak Razor söz dizimini kullanır. RazorBiçimlendirme diline alışkın değilseniz, devam etmeden önce okumanız önerilir <xref:mvc/views/razor> .

Söz dizimi üzerindeki içeriğe erişirken Razor , aşağıdaki bölümlere özel bir dikkat ödeyin:

* [Yönergeler](xref:mvc/views/razor#directives): `@` -genellikle bileşen biçimlendirmesinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.
* [Yönerge öznitelikleri](xref:mvc/views/razor#directive-attributes): `@` -önek olarak bileşen öğelerinin ayrıştırılma veya işlev şeklini değiştiren ayrılmış anahtar sözcükler.

### <a name="names"></a>Adlar

Bir bileşenin adı, büyük harfle başlamalıdır. Örneğin, `MyCoolComponent.razor` geçerlidir ve `myCoolComponent.razor` geçersizdir.

### <a name="routing"></a>Yönlendirme

Uygulamasında yönlendirme, Blazor uygulamadaki her erişilebilir bileşene bir rota şablonu sağlanarak elde edilir. Razor [`@page`][9] Yönergeyle bir dosya derlendiğinde, oluşturulan sınıfa <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> yol şablonunu belirten bir değer verilir. Çalışma zamanında, yönlendirici bileşen sınıflarını bir ile arar <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ve hangi bileşenin Istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>İşaretleme

Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir *Razor* . Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür. Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.

Bileşen sınıfının üyeleri bir [`@code`][1] blokta tanımlanır. [`@code`][1]Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir. Birden çok [`@code`][1] blok izin verilir.

Bileşen üyeleri, ile başlayan C# ifadeleri kullanılarak bileşenin işleme mantığının bir parçası olarak kullanılabilir `@` . Örneğin, bir C# alanı alan adının önüne eklenerek işlenir `@` . Aşağıdaki örnek değerlendirilir ve işler:

* `headingFontStyle`için CSS özellik değerine `font-style` .
* `headingText``<h1>`öğenin içeriğine.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur. Blazorardından, yeni işleme ağacını önceki bir değerle karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde yapılan tüm değişiklikleri uygular.

Bileşenler, normal C# sınıflarıdır ve bir proje içinde herhangi bir yere yerleştirilebilir. Web sayfalarını üreten bileşenler genellikle `Pages` klasöründe bulunur. Sayfa olmayan bileşenler sıklıkla `Shared` klasöre veya projeye eklenen özel bir klasöre yerleştirilir.

### <a name="namespaces"></a>Ad Alanları

Genellikle, bir bileşenin ad alanı uygulamanın kök ad alanından ve uygulamanın içindeki konum (klasör) ile türetilir. Uygulamanın kök ad alanı ise `BlazorSample` ve `Counter` bileşen `Pages` klasöründe bulunuyorsa:

* `Counter`Bileşenin ad alanı `BlazorSample.Pages` .
* Bileşenin tam nitelikli tür adı `BlazorSample.Pages.Counter` .

Bileşenleri tutan özel klasörler için, [`@using`][2] üst bileşene veya uygulamanın dosyasına bir yönerge ekleyin `_Imports.razor` . Aşağıdaki örnek, klasördeki bileşenleri kullanılabilir hale getirir `Components` :

```razor
@using BlazorSample.Components
```

Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [`@using`][2] yönergeyi gerektirmez:

```razor
<BlazorSample.Components.MyComponent />
```

İle yazılmış bir bileşenin ad alanı, Razor tabanlıdır (öncelik sırasına göre):

* [`@namespace`][8]Razordosya ( `.razor` ) biçimlendirmesinde atama () `@namespace BlazorSample.MyNamespace` .
* Proje, `RootNamespace` Proje dosyasında ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Proje dosyasının dosya adından () alınan proje adı `.csproj` ve proje kökünden bileşen yolu. Örneğin, çerçeve `{PROJECT ROOT}/Pages/Index.razor` `BlazorSample.csproj` ad alanına () çözümler `BlazorSample.Pages` . Bileşenler C# ad bağlama kurallarını izler. `Index`Bu örnekteki bileşen için, kapsamdaki bileşenler tüm bileşenlerdir:
  * Aynı klasörde, `Pages` .
  * Proje kökündeki, açıkça farklı bir ad alanı belirtmeyen bileşenler.

> [!NOTE]
> `global::`Nitelendirme desteklenmiyor.
>
> Diğer ad [`using`](/dotnet/csharp/language-reference/keywords/using-statement) deyimleri (örneğin,) ile bileşenleri içeri aktarma `@using Foo = Bar` desteklenmiyor.
>
> Kısmen nitelenmiş adlar desteklenmez. Örneğin, `@using BlazorSample` `NavMenu` ile bileşeni () ekleme ve başvuru `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` desteklenmez.

### <a name="partial-class-support"></a>Kısmi sınıf desteği

Razorbileşenler kısmi sınıflar olarak oluşturulur. Razorbileşenler aşağıdaki yaklaşımlardan birini kullanarak yazılır:

* C# kodu, [`@code`][1] tek bir dosyada HTML işaretlemesi ve kodu olan bir blokta tanımlanmıştır Razor . BlazorŞablonlar, Razor Bu yaklaşımı kullanarak bileşenlerini tanımlar.
* C# kodu, kısmi sınıf olarak tanımlanmış bir arka plan kod dosyasına yerleştirilir.

Aşağıdaki örnek, `Counter` [`@code`][1] bir şablondan oluşturulan uygulamada bir blok içeren varsayılan bileşeni gösterir Blazor . HTML Markup, Razor Code ve C# kodu aynı dosyada:

`Pages/Counter.razor`:

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

`Counter`Bileşen ayrıca kısmi bir sınıf içeren bir arka plan kod dosyası kullanılarak oluşturulabilir:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
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

Gerekli olan ad alanlarını kısmi sınıf dosyasına gereken şekilde ekleyin. Bileşenler tarafından kullanılan tipik ad alanları Razor şunlardır:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2]`_Imports.razor`dosyadaki yönergeler Razor `.razor` , C# dosyalarına () değil, yalnızca dosyalar () için geçerlidir `.cs` .

### <a name="specify-a-base-class"></a>Temel sınıf belirtin

[`@inherits`][6]Yönergesi bir bileşen için temel sınıf belirtmek üzere kullanılabilir. Aşağıdaki örnek, bileşenin `BlazorRocksBase` özelliklerini ve yöntemlerini sağlamak için bir bileşenin bir temel sınıfı nasıl devralmasını gösterir. Taban sınıfın türevi olması gerekir <xref:Microsoft.AspNetCore.Components.ComponentBase> .

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

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

### <a name="use-components"></a>Bileşenleri kullanma

Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir. Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.

İçinde aşağıdaki biçimlendirme `Pages/Index.razor` bir örneği işler `HeadingComponent` :

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır. [`@using`][2]Bileşenin ad alanı için bir yönerge eklemek, bileşeni, uyarıyı çözen şekilde kullanılabilir hale getirir.

## <a name="parameters"></a>Parametreler

### <a name="route-parameters"></a>Rota parametreleri

Bileşenler, yönergede belirtilen yol şablonundan rota parametreleri alabilir [`@page`][9] . Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

İsteğe bağlı parametreler desteklenmez, bu nedenle [`@page`][9] Önceki örnekte iki yönergeler uygulanır. İlki, bir parametre olmadan bileşene gezinmesine izin verir. İkinci [`@page`][9] yönerge, `{text}` route parametresini alır ve değeri `Text` özelliğine atar.

*Catch-all* `*` / `**` Birden çok klasör sınırlarındaki yolu yakalayan catch-all parametre sözdizimi () **,** Razor bileşenlerinde ( `.razor` ) desteklenmez.

### <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler, bileşen sınıfında özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) . Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Örnek uygulamadan aşağıdaki örnekte, `ParentComponent` `Title` öğesinin özelliğinin değerini ayarlar `ChildComponent` .

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Kendi *bileşen parametrelerine*yazan bileşenler oluşturmayın, bunun yerine özel bir alan kullanın. Daha fazla bilgi için [kendi parametre özelliklerine yazan bileşenleri oluşturma](#dont-create-components-that-write-to-their-own-parameter-properties) bölümüne bakın.

## <a name="child-content"></a>Alt içerik

Bileşenler, başka bir bileşenin içeriğini ayarlayabilir. Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.

Aşağıdaki örnekte, öğesinin `ChildComponent` `ChildContent` <xref:Microsoft.AspNetCore.Components.RenderFragment> işlemek için bir kullanıcı arabirimi segmentini temsil eden öğesini temsil eden bir özelliği vardır. Değeri, `ChildContent` bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır. Değeri, `ChildContent` ana bileşenden alınır ve önyükleme paneli içinde işlenir `panel-body` .

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> İçeriği alan özelliğin <xref:Microsoft.AspNetCore.Components.RenderFragment> `ChildContent` kural tarafından adlandırılması gerekir.

`ParentComponent`Örnek uygulamadaki ' de `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilirsiniz `<ChildComponent>` .

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

Alt Blazor bileşenin içeriğinde artırma döngüsü değişkeni kullanılıyorsa, alt içeriği işleyen bir döngü içindeki işleme bileşenleri `for` yerel bir dizin değişkeni gerektirir:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Alternatif olarak, `foreach` ile bir döngüsü kullanın <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Öznitelik döndürme ve rastgele parametreler

Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir. Ek öznitelikler bir sözlükte yakalanıp, sonra bileşen yönergesi kullanılarak işlendiğinde bir *öğe üzerine bırakılabilir* [`@attributes`][3] Razor . Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır. Örneğin, `<input>` çok sayıda parametreyi destekleyen bir için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.

Aşağıdaki örnekte, ilk `<input>` öğesi ( `id="useIndividualParams"` ) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe ( `id="useAttributesDict"` ) öznitelik splatesini kullanır:

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

Parametrenin türü `IEnumerable<KeyValuePair<string, object>>` dize anahtarlarıyla gerçekleştirmelidir. `IReadOnlyDictionary<string, object>`Bu senaryoda ayrıca bir seçenek de vardır.

`<input>`Her iki yaklaşımın de kullanıldığı işlenen öğeler aynıdır:

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

Rastgele öznitelikleri kabul etmek için [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) özelliği olarak ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Üzerindeki özelliği, [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar. Bir bileşen yalnızca ile tek bir parametre tanımlayabilir <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . İle kullanılan özellik türü <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `Dictionary<string, object>` dize anahtarlarıyla atanabilir olmalıdır. `IEnumerable<KeyValuePair<string, object>>``IReadOnlyDictionary<string, object>`Ayrıca, Bu senaryodaki seçenekler de vardır.

[`@attributes`][3]Öğe özniteliklerinin konumuna göreli konumu önemlidir. Öğe üzerinde ne zaman bırakıldığında [`@attributes`][3] , öznitelikler sağdan sola (son-ilk) işlenir. Bir bileşeni tüketen bir bileşen için aşağıdaki örneği göz önünde bulundurun `Child` :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child`Bileşenin `extra` özniteliği öğesinin sağına ayarlanır [`@attributes`][3] . `Parent` `<div>` `extra="5"` Öznitelikler sağdan sola (en son) işlenmediğinden, bileşen tarafından işlenen ek öznitelik aracılığıyla geçirilir:

```html
<div extra="5" />
```

Aşağıdaki örnekte, `extra` ve sırası [`@attributes`][3] `Child` bileşen tarafından tersine çevrilir `<div>` :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Bileşendeki işlenen `<div>` `Parent` `extra="10"` ek öznitelik ile geçirildiğinde şunları içerir:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Bileşenlere başvuruları yakala

Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya gibi komutları bu örneğe verebilirsiniz `Show` `Reset` . Bir bileşen başvurusunu yakalamak için:

* [`@ref`][4]Alt bileşene bir öznitelik ekleyin.
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
> `loginDialog`Değişken yalnızca bileşen işlendikten sonra ve çıktısı öğesi içerdiğinde doldurulur `MyLoginDialog` . Bileşen işlenene kadar, başvurulmasına hiçbir şey yok.
>
> Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için, [ `OnAfterRenderAsync` veya `OnAfterRender` yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.
>
> Bir olay işleyicisiyle bir başvuru değişkeni kullanmak için bir lambda ifadesi kullanın veya [ `OnAfterRenderAsync` ya da `OnAfterRender` yöntemlerinde](xref:blazor/components/lifecycle#after-component-render)olay işleyicisi temsilcisini atayın. Bu, başvuru değişkeninin olay işleyicisi atanmadan önce atanmasını sağlar.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Bir döngüdeki bileşenlere başvurmak için bkz. [birden çok benzer alt bileşene başvuruları yakalama (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir JavaScript birlikte çalışma özelliği değildir. Bileşen başvuruları JavaScript koduna geçirilmiyor. Bileşen başvuruları yalnızca .NET kodunda kullanılır.

> [!NOTE]
> Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.** Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın. Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.

## <a name="synchronization-context"></a>Eşitleme bağlamı

Blazor<xref:System.Threading.SynchronizationContext>yürütmenin tek bir mantıksal iş parçacığını zorlamak için bir eşitleme bağlamı () kullanır. Bir bileşenin [yaşam döngüsü yöntemleri](xref:blazor/components/lifecycle) ve tarafından oluşturulan tüm olay geri çağırmaları Blazor eşitleme bağlamında yürütülür.

Blazor Server, tek iş parçacıklı bir ortamı öykünmeye çalışır ve bu sayede tek iş parçacıklı bir tarayıcıda WebAssembly modeliyle yakından eşleşir. Belirli bir zamanda, iş, tek bir mantıksal iş parçacığının izlenimi vererek tam olarak bir iş parçacığında gerçekleştirilir. Aynı anda iki işlem yürütülmez.

### <a name="avoid-thread-blocking-calls"></a>İş parçacığı engelleme çağrılarını önleyin

Genellikle, aşağıdaki yöntemleri çağırmayın. Aşağıdaki yöntemler iş parçacığını engeller ve bu nedenle, temel tamamlanana kadar uygulamanın çalışmaya devam ettirilmesi engellenir <xref:System.Threading.Tasks.Task> :

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır

Bir bileşenin, Zamanlayıcı veya diğer bildirimler gibi bir dış olay temel alınarak güncellenmesi gerekir, bu `InvokeAsync` yöntemi, Blazor eşitleme bağlamına dağımakta olan yöntemini kullanın. Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:

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

Şunu kaydedin `NotifierService` :

* İçinde Blazor WebAssembly , hizmeti şu şekilde ayrı kaydedin `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* ' De Blazor Server , hizmeti kapsamında şu şekilde kaydedin `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

`NotifierService`Bir bileşeni güncelleştirmek için kullanın:

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

Önceki örnekte, `NotifierService` bileşen `OnNotify` metodunu Blazor eşitleme bağlamı dışında çağırır. `InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@Öğe ve bileşenlerin korunmasını denetlemek için anahtar kullanın

Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor Bu, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir. Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.

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

Koleksiyonun içeriği, `People` ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir. Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı parametre değerleri almak için değişebilir `Details` . Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir. Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.

Eşleme işlemi, [`@key`][5] Directive özniteliğiyle denetlenebilir. [`@key`][5], anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:

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

`People`Koleksiyon değiştiğinde, yayılma algoritması `<DetailsEditor>` örnekler ve örnekler arasındaki ilişkilendirmeyi korur `person` :

* Bir, `Person` `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır. Diğer örnekler değişmeden bırakılır.
* Listedeki bir `Person` konuma eklenirse, `<DetailsEditor>` ilgili konuma bir yeni örnek eklenir. Diğer örnekler değişmeden bırakılır.
* `Person`Girişler yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.

Bazı senaryolarda, kullanımı [`@key`][5] rerendering karmaşıklığını en aza indirir ve odak konumu gıbı Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.

> [!IMPORTANT]
> Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir. Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.

### <a name="when-to-use-key"></a>Anahtar ne zaman kullanılır? \@

Genellikle, [`@key`][5] her bir liste işlendiğinde (örneğin, bir [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) bloğunda) ve tanımlamak için uygun bir değer varsa, bu durum kullanılır [`@key`][5] .

Bir [`@key`][5] Blazor nesne değiştiğinde bir öğeyi veya bileşen alt ağacını korumayı engellemek için ' i de kullanabilirsiniz:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson`Değişiklik olursa, [`@key`][5] öznitelik yönergesi Blazor tüm `<div>` ve alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar. Değişiklik sırasında hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir `@currentPerson` .

### <a name="when-not-to-use-key"></a>Anahtar ne zaman kullanılmaz? \@

İle yayılma yaparken bir performans maliyeti vardır [`@key`][5] . Performans maliyeti büyük değildir, ancak yalnızca [`@key`][5] öğenin veya bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.

[`@key`][5]Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur. Kullanmanın tek avantajı [`@key`][5] model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.

### <a name="what-values-to-use-for-key"></a>Anahtar için kullanılacak değerler \@

Genellikle, için aşağıdaki değer türlerinden birini sağlamak mantıklı olur [`@key`][5] :

* Model nesne örnekleri (örneğin, `Person` Önceki örnekte olduğu gibi). Bu, nesne başvurusu eşitliğine göre koruma sağlar.
* Benzersiz tanımlayıcılar (örneğin, veya türündeki birincil anahtar değerleri `int` `string` `Guid` ).

Bu değerlerin çakışmayın için kullanıldığından emin olun [`@key`][5] . Aynı üst öğe içinde çakışan değerler algılanırsa, Blazor eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden bir özel durum oluşturur. Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Kendi parametre özelliklerine yazan bileşenler oluşturmayın

Parametreleri aşağıdaki koşullarda üzerine yazılır:

* Bir alt bileşenin içeriği ile işlenir <xref:Microsoft.AspNetCore.Components.RenderFragment> .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>üst bileşende çağrılır.

Üst bileşen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> çağrıldığında ve alt bileşene yeni parametre değerleri sağlandığında parametreler sıfırlanır.

Aşağıdaki bileşeni göz önünde bulundurun `Expander` :

* Alt içeriği işler.
* Bileşen parametresiyle alt içeriğin gösterilmesini değiştirir.

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (<code>Expanded</code> = @Expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
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

`Expander`Bileşen, çağıraetkileyebilecek bir üst bileşene eklenir <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Başlangıçta, `Expander` bileşenleri özellikleri bir kez değiştiğinde bağımsız olarak davranır `Expanded` . Alt bileşenler, durumlarını beklendiği gibi korur. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>Üst öğede çağrıldığında, `Expanded` ilk alt bileşenin parametresi ilk değeri () olarak döndürülür `true` . İkinci `Expander` `Expanded` bileşende hiçbir alt içerik işlenmediğinden ikinci bileşenin değeri sıfırlanmıyor.

Önceki senaryodaki durumu korumak için bileşen içindeki *özel bir alanı* kullanarak, onun geçiş `Expander` durumunu koruyun.

Aşağıdaki düzeltilen `Expander` bileşen:

* Üst öğeden `Expanded` bileşen parametre değerini kabul eder.
* *private field* `expanded` [OnInitialized olaydaki](xref:blazor/components/lifecycle#component-initialization-methods)bir özel alana () bileşen parametre değerini atar.
* İç geçiş durumunu korumak için özel alanını kullanır.

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (<code>expanded</code> = @expanded)</h2>
        </div>

        @if (expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

## <a name="apply-an-attribute"></a>Öznitelik uygulama

Öznitelikler Razor , yönergeyle birlikte bileşenlere uygulanabilir [`@attribute`][7] . Aşağıdaki örnek, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşen sınıfına özniteliğini uygular:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Koşullu HTML öğesi öznitelikleri

HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir. Değer `false` veya ise `null` , öznitelik işlenmez. Değer ise `true` , öznitelik küçültülmüş olarak işlenir.

Aşağıdaki örnekte, `IsCompleted` `checked` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini belirler:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted`İse `true` , onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" checked />
```

`IsCompleted`İse `false` , onay kutusu şu şekilde işlenir:

```html
<input type="checkbox" />
```

Daha fazla bilgi için bkz. <xref:mvc/views/razor>.

> [!WARNING]
> [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons).NET türü bir olduğunda, gibi bazı HTML öznitelikleri düzgün şekilde çalışmaz `bool` . Bu durumlarda, `string` yerine bir tür kullanın `bool` .

## <a name="raw-html"></a>Ham HTML

Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir. Ham HTML işlemek için, HTML içeriğini bir değerde sarın `MarkupString` . Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.

> [!WARNING]
> Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!

Aşağıdaki örnek, `MarkupString` bir bileşenin işlenmiş çıktısına STATIK HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razorşablondan

İşleme parçaları, Razor şablon sözdizimi kullanılarak tanımlanabilir. RazorŞablonlar, UI parçacığı tanımlamanın ve aşağıdaki biçimi varsayacak bir yoldur:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Aşağıdaki örnek, <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> bir bileşeni doğrudan bir bileşende nasıl belirtdiğini ve işleneceğini gösterir. Oluşturma parçaları, [şablonlu bileşenlere](xref:blazor/components/templated-components)bağımsız değişken olarak da geçirilebilir.

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

## <a name="static-assets"></a>Statik varlıklar

BlazorProjenin [ `web root (wwwroot)` klasörü](xref:fundamentals/index#web-root)altına statik varlıklar yerleştirmekte olan ASP.NET Core uygulama kuralına uyar.

`/`Statik bir varlık için Web köküne başvurmak üzere temel göreli bir yol () kullanın. Aşağıdaki örnekte, bu `logo.png` klasörde fiziksel olarak bulunur `{PROJECT ROOT}/wwwroot/images` :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razorbileşenler, **not** tilde işareti gösterimini ( `~/` ) desteklemez.

Uygulamanın temel yolunu ayarlama hakkında daha fazla bilgi için bkz <xref:blazor/host-and-deploy/index#app-base-path> ..

## <a name="tag-helpers-arent-supported-in-components"></a>Etiket Yardımcıları bileşenlerde desteklenmiyor

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)Razorbileşenlerde ( `.razor` Dosyalar) desteklenmez. ' De etiket Yardımcısı benzeri işlevsellik sağlamak için Blazor , etiket Yardımcısı ile aynı işlevselliğe sahip bir bileşen oluşturun ve bunun yerine bileşeni kullanın.

## <a name="scalable-vector-graphics-svg-images"></a>Ölçeklenebilir vektör grafik (SVG) görüntüleri

İşleme Blazor HTML, ölçeklenebilir vektör grafik (SVG) görüntüleri de dahil olmak üzere tarayıcıda desteklenen görüntüler ( `.svg` ) etiketi aracılığıyla desteklenir `<img>` :

```html
<img alt="Example image" src="some-image.svg" />
```

Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının () CSS kurallarında desteklenir `.css` :

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez. Bir `<svg>` etiketi doğrudan bir bileşen dosyasına ( `.razor` ) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir. Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve [`@bind`][10] bazı SVG etiketleriyle kullanılamaz. Daha fazla bilgi için bkz. [Içindeki SVG desteği Blazor (DotNet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/security/server/threat-mitigation>: Blazor Server Kaynak tükenmesi ile Çekişmek zorunda olan uygulamalar oluşturmaya yönelik yönergeler içerir.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
