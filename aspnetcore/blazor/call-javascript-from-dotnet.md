---
title: ASP.NET Core'daki .NET yöntemlerinden JavaScript işlevlerini arayınBlazor
author: guardrex
description: Uygulamalardaki Blazor .NET yöntemlerinden JavaScript işlevlerini nasıl çağırılamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977021"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>ASP.NET Core'daki .NET yöntemlerinden JavaScript işlevlerini arayınBlazor

Javier [Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27)ve Luke [Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bir Blazor uygulama JavaScript işlevlerinden .NET yöntemlerinden ve .NET yöntemlerinden JavaScript işlevlerini çağırabilir. Bu senaryolar *JavaScript birlikte çalışabilirlik* *(JS interop)* olarak adlandırılır.

Bu makale, .NET'ten JavaScript işlevlerini çağırarak kapsar. JavaScript'ten .NET yöntemlerini nasıl arayacağıhakkında bilgi için bkz. <xref:blazor/call-dotnet-from-javascript>

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

.NET'ten JavaScript'e çağrı `IJSRuntime` yapmak için soyutlamayı kullanın. JS interop çağrıları vermek `IJSRuntime` için, bileşeninize soyutlama enjekte edin. Yöntem, `InvokeAsync<T>` herhangi bir sayıda JSON-serializable bağımsız değişkeni ile birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır. İşlev tanımlayıcısı genel kapsama göredir (`window`). Aramak `window.someScope.someFunction`isterseniz, tanımlayıcı `someScope.someFunction`. Çağrılmadan önce işlevi kaydetmenize gerek yoktur. İade türü `T` de JSON serileştirilebilir olmalıdır. `T`döndürülen JSON türüyle en iyi eşlemleri içeren .NET türüyle eşleşmelidir.

Ön Blazor oluşturma etkinleştirilmiş Sunucu uygulamaları için, ilk ön oluşturma sırasında JavaScript'e çağrı yapmak mümkün değildir. JavaScript interop çağrıları tarayıcı ile bağlantı kurulana kadar ertelenmelidir. Daha fazla bilgi için, [sunucu Blazor uygulaması nın ön oluşturma](#detect-when-a-blazor-server-app-is-prerendering) bölümü ne zaman algıla'ya bakın.

Aşağıdaki örnek, JavaScript tabanlı kod çözücü [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)dayanmaktadır. Örnek, C# yönteminden javascript işlevinin nasıl çağrıldığını gösterir. JavaScript işlevi C# yönteminden bir bayt dizisini kabul eder, diziyi çözer ve metni görüntülenmek üzere bileşene döndürür.

*wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor `TextDecoder` `<head>` öğesi içinde, geçirilen bir dizide şifreyi çözmek ve çözülmüş değeri döndürmek için kullanan bir JavaScript işlevi sağlayın:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Önceki örnekte gösterilen kod gibi JavaScript kodu, komut dosyası dosyasına atıfta bulunularak javascript dosyasından *(.js)* de yüklenebilir:

```html
<script src="exampleJsInterop.js"></script>
```

Aşağıdaki bileşen:

* `convertArray` Bir bileşen düğmesi **(Diziyi Dönüştür)** seçildiğinde JavaScript işlevini kullanır. `JSRuntime`
* JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dize dönüştürülür. Dize görüntülenmek üzere bileşene döndürülür.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Soyutlamayı `IJSRuntime` kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:

* Soyutlamayı `IJSRuntime` Jilet bileşenine enjekte edin (*.razor):*

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor öğesi `handleTickerChanged` içinde `<head>` bir JavaScript işlevi sağlar. İşlev ile `IJSRuntime.InvokeVoidAsync` denir ve bir değer döndürmez:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Soyutlamayı `IJSRuntime` bir sınıfa enjekte edin (*.cs):*

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *wwwroot/index.html* (WebAssembly)Blazor veya *Pages/_Host.cshtml* (Server)Blazor öğesi `handleTickerChanged` içinde `<head>` bir JavaScript işlevi sağlar. İşlev ile `JSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma `[Inject]` için özniteliği kullanın:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Bu konuya eşlik eden istemci tarafındaki örnek uygulamada, kullanıcı girişi almak ve hoş geldiniz iletisi görüntülemek için DOM ile etkileşime geçen uygulama için iki JavaScript işlevi kullanılabilir:

* `showPrompt`&ndash; Kullanıcı girişi (kullanıcının adı) kabul etmek için bir istem üretir ve adı arayana döndürür.
* `displayWelcome`&ndash; Arayankişiden bir DOM nesnesine hoş `id` geldiniz `welcome`iletisi atar.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript `<script>` dosyasına başvuran etiketi *wwwroot/index.html* dosyasınaBlazor (WebAssembly) veya *Pages/_Host.cshtml* dosyasına (Sunucu)Blazor yerleştirin.

*wwwroot/index.html* Blazor (WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Sayfa/_Host.cshtml* Blazor (Sunucu):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Etiket dinamik olarak `<script>` güncelleştirilemediği için `<script>` bileşen dosyasına etiket yerleştirmeyin.

.NET yöntemleri javascript fonksiyonları ile interop *örnekjsInterop.js* dosyaarayarak `IJSRuntime.InvokeAsync<T>`.

Soyutlama, `IJSRuntime` Sunucu senaryolarına izin vermek Blazor için bir senkrondur. Uygulama bir Blazor WebAssembly uygulamasıysa ve bir JavaScript işlevini eşzamanlı olarak çağırmak `IJSInProcessRuntime` istiyorsanız, bunun yerine aşağı döküm ve çağrı. `Invoke<T>` Çoğu JS interop kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olduğundan emin olmak için async API'lerini kullanmanızı öneririz.

Örnek uygulama JS interop göstermek için bir bileşen içerir. Bileşen:

* JavaScript komut istemi ile kullanıcı girişi alır.
* Metni işleme için bileşene döndürür.
* Hoş geldiniz iletisi görüntülemek için DOM ile etkileşimedebilen ikinci bir JavaScript işlevini çağırır.

*Sayfa/JSInterop.razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Bileşenin **Trigger JavaScript Komut İstedüğmesi** seçilerek yürütüldüğünde, `showPrompt` `TriggerJsPrompt` *wwwroot/exampleJsInterop.js* dosyasında sağlanan JavaScript işlevi çağrılır.
1. İşlev, `showPrompt` HTML kodlu kullanıcı girişi (kullanıcı adı) kabul eder ve bileşene döndürülür. Bileşen, kullanıcının adını yerel bir değişkende `name`depolar.
1. Depolanan `name` dize, hoş geldiniz iletisini bir başlık etiketine işleyen `displayWelcome`bir JavaScript işlevine geçirilen bir karşılama iletisine dahil edilir.

## <a name="call-a-void-javascript-function"></a>Geçersiz bir JavaScript işlevini arayın

[Void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [tanımlanmamış](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren `IJSRuntime.InvokeVoidAsync`JavaScript işlevleri .

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Bir Blazor Sunucu uygulamasının ne zaman önceden oluşturulaca-sonuç veriyi algıla
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Öğelere yapılan başvuruları yakalama

Bazı JS interop senaryoları HTML öğelerine başvuru gerektirir. Örneğin, bir UI kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya komut benzeri API'leri bir öğe üzerinde aramanız `focus` gerekebilir. `play`

Aşağıdaki yaklaşımı kullanarak bir bileşendeki HTML öğelerine yapılan başvuruları yakalayın:

* HTML `@ref` öğesine bir öznitelik ekleyin.
* Adı öznitelik `ElementReference` değeriyle eşleşen bir `@ref` tür alanı tanımlayın.

Aşağıdaki örnek, `username` `<input>` öğeye bir başvuru yakalama gösterir:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Yalnızca etkileşime girmeyen boş bir öğenin içeriğini mutasyona Blazoruğratmak için bir öğe başvurusu kullanın. Bu senaryo, üçüncü taraf api öğesine içerik sağladığında yararlıdır. Öğeile Blazor etkileşime girmediği için, 'öğenin temsili Blazorile DOM arasında bir çakışma olasılığı yoktur.
>
> Aşağıdaki örnekte, bu öğenin liste öğeleridoldurmak için DOM ile`ul` Blazor etkileşimde çünkü sıralanmamış listenin içeriğini mutasyona`<li>`uğratmak için *tehlikelidir* ( ):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> JS interop öğenin `MyList` içeriğini mutasyona Blazor uğrar ve elemandiffs uygulamak için çalışırsa, diffs DOM eşleşmez.

.NET kodu söz konusu olduğunda, opak `ElementReference` bir tutamaçtır. Yapabileceğiniz *tek* şey JS interop üzerinden JavaScript koduna `ElementReference` geçmektir. Bunu yaptığınızda, JavaScript yan kodu `HTMLElement` normal DOM API'leri ile kullanabileceği bir örnek alır.

Örneğin, aşağıdaki kod, bir öğenin odağının ayarlanmasını sağlayan bir .NET uzantı yöntemi tanımlar:

*örnekJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Değer vermeyen bir JavaScript işlevini çağırmak için `IJSRuntime.InvokeVoidAsync`. Aşağıdaki kod yakalanan `ElementReference`önceki JavaScript işlevi arayarak kullanıcı adı girişi üzerinde odak ayarlar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Uzantı yöntemini kullanmak `IJSRuntime` için, örneği alan statik bir uzantı yöntemi oluşturun:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Yöntem `Focus` doğrudan nesne üzerinde çağrılır. Aşağıdaki örnek, yöntemin `Focus` `JsInteropClasses` ad alanından kullanılabildiğini varsayar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Değişken `username` yalnızca bileşen işlendikten sonra doldurulur. Doldurulmamış bir `ElementReference` JavaScript koduna aktarılırsa, JavaScript kodu `null`. Bileşen işleme bittikten sonra eleman başvurularını işlemek için (bir öğeye ilk odağı ayarlamak için) [OnAfterRenderAsync veya OnAfterRender bileşen yaşam döngüsü yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.

Genel türlerle çalışırken ve bir değer döndürerken, [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1)kullanın:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`türü olan nesneye doğrudan çağrılır. Aşağıdaki örnek, `GenericMethod` `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Bileşenler arasında referans öğeleri

Bir `ElementReference` bileşenin `OnAfterRender` yönteminde geçerli olduğu garanti edilir (ve `struct`bir öğe başvurusu birdir), bu nedenle bir öğe başvurusu bileşenler arasında geçirilemez.

Bir üst bileşenin bir öğe referansını diğer bileşenler için kullanılabilir hale getirebilmek için, üst bileşen şunları yapabilir:

* Alt bileşenlerin geri aramaları kaydetmesine izin verin.
* Geçen öğe başvurusuyla `OnAfterRender` olay sırasında kayıtlı geri aramaları çağırın. Dolaylı olarak, bu yaklaşım alt bileşenlerin üst öğe başvurusu ile etkileşim sağlar.

Aşağıdaki Blazor WebAssembly örneği yaklaşımı göstermektedir.

`<head>` *wwwroot/index.html olarak:*

```html
<style>
    .red { color: red }
</style>
```

`<body>` *wwwroot/index.html olarak:*

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Sayfalar/Index.razor* (üst bileşen):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Sayfa/Index.razor.cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Paylaşılan/SurveyPrompt.razor* (alt bileşeni):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Paylaşılan/SurveyPrompt.razor.cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Harden JS interop aramaları

JS interop ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak kabul edilmelidir. Varsayılan olarak, Blazor bir Sunucu uygulaması bir dakika sonra sunucudaki JS interop çağrılarını kez times. Bir uygulama 10 saniye gibi daha agresif bir zaman adabına tahammül edebilirse, zaman anına aşağıdaki yaklaşımlardan birini kullanarak ayarlayın:

* Genel `Startup.ConfigureServices`olarak, zaman anına belirtin:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Bileşen kodunda çağırma başına, tek bir arama zaman ayarı belirtebilir:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Kaynak tükenmesi hakkında daha <xref:security/blazor/server>fazla bilgi için bkz.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Dairesel nesne başvurularından kaçının

Dairesel başvurular içeren nesneler istemcide aşağıdakiler için serileştirilemez:

* .NET yöntemi çağırır.
* İade türü dairesel referansları olduğunda JavaScript yöntemi C#'dan çağrı verir.

Daha fazla bilgi için aşağıdaki sorunlara bakın:

* [Dairesel başvurular desteklenmez, iki tane alın (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Öneri: Serileştirme yaparken dairesel başvuruları işlemek için mekanizma ekleme (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor örnek (dotnet/AspNetCore GitHub deposu, 3.1 sürüm şubesi)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
