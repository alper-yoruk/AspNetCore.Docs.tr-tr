---
title: ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırınBlazor
author: guardrex
description: Blazor Uygulamalarda .net metotlarından JavaScript işlevlerini çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2f12c2a05ef510c38f407ccd69f748aceafd55de
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767180"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırınBlazor

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir. Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.

Bu makalede, .NET 'ten JavaScript işlevlerini çağırma ele alınmaktadır. JavaScript 'ten .NET yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:blazor/call-dotnet-from-javascript>..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

.NET 'ten JavaScript 'e çağrı yapmak için `IJSRuntime` soyutlamayı kullanın. JS birlikte çalışma çağrıları vermek için, bu `IJSRuntime` soyutlamayı bileşeninizdeki ekler. Yöntemi `InvokeAsync<T>` , herhangi BIR sayıda JSON seri hale getirilebilir bağımsız değişkenle birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır. İşlev tanımlayıcısı genel kapsama (`window`) göredir. Çağırmak `window.someScope.someFunction`isterseniz, tanımlayıcı olur `someScope.someFunction`. Çağrılmadan önce işlevi kaydetmeniz gerekmez. Dönüş türünün `T` de JSON seri hale getirilebilir olması gerekir. `T`döndürülen JSON türüyle en iyi eşleşen .NET türüyle eşleşmelidir.

Prerendering Blazor özellikli sunucu uygulamaları için, ilk prerendering sırasında JavaScript 'e çağrı yapılamaz. JavaScript birlikte çalışma çağrılarının, tarayıcıyla bağlantı kurulana kadar ertelenmesi gerekir. Daha fazla bilgi için bkz. [ Blazor sunucu uygulamasının ne zaman prerendering olduğunu Algıla](#detect-when-a-blazor-server-app-is-prerendering) bölümü.

Aşağıdaki örnek, JavaScript tabanlı bir kod çözücü olan [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)tabanlıdır. Örnek, C# yönteminden bir JavaScript işlevinin nasıl çağrılacağını gösterir. JavaScript işlevi bir C# yönteminden bir bayt dizisi kabul eder, dizinin kodunu çözer ve görüntülenecek metni bileşene döndürür.

*Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde, geçirilen bir dizinin kodunu çözmek için kullanılan `TextDecoder` bir JavaScript işlevi sağlayın ve kodu çözülen değeri döndürün:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Önceki örnekte gösterilen kod gibi JavaScript kodu, betik dosyasına yönelik bir başvuruya sahip bir JavaScript dosyasından (*. js*) de yüklenebilir:

```html
<script src="exampleJsInterop.js"></script>
```

Aşağıdaki bileşen:

* Bir bileşen `convertArray` düğmesi ( `JSRuntime` **diziyi Dönüştür**) seçildiğinde JavaScript işlevini çağırır.
* JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dizeye dönüştürülür. Dize, görüntüleme için bileşene döndürülür.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

`IJSRuntime` Soyutlamayı kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:

* `IJSRuntime` Özet Razor bileşene (*. Razor*) ekleme:

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde bir `handleTickerChanged` JavaScript işlevi sağlayın. İşlevi ile `IJSRuntime.InvokeVoidAsync` çağrılır ve bir değer döndürmez:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* `IJSRuntime` Soyutlamayı bir sınıfa (*. cs*) Ekle:

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde bir `handleTickerChanged` JavaScript işlevi sağlayın. İşlevi ile `JSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [Buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma için `[Inject]` özniteliğini kullanın:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Bu konuya eşlik eden istemci tarafı örnek uygulamada, Kullanıcı girişi almak ve bir hoş geldiniz iletisi göstermek üzere DOM ile etkileşime geçen uygulama için iki JavaScript işlevi mevcuttur:

* `showPrompt`&ndash; Kullanıcı girişini kabul etmek için bir istem üretir (kullanıcının adı) ve arayan adına adı döndürür.
* `displayWelcome`&ndash; ' A SAHIP `id` bir DOM nesnesine çağırandan bir hoş geldiniz iletisi atar `welcome`.

*Wwwroot/Examplejsınterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript dosyasına `<script>` başvuran etiketi *Wwwroot/index.html* File (Blazor webassembly) veya *Pages/_Host. cshtml* dosyası (Blazor sunucu) içine yerleştirin.

*Wwwroot/index.html* (Blazor webassembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host. cshtml* (Blazor sunucu):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Etiket dinamik olarak `<script>` güncelleştirilemediğinden bir bileşen dosyasına etiket yerleştirmeyin. `<script>`

.NET yöntemleri, çağırarak `IJSRuntime.InvokeAsync<T>`, *Examplejsınterop. js* dosyasındaki JavaScript işlevleriyle birlikte çalışır.

Soyutlama `IJSRuntime` , Blazor sunucu senaryolarına izin vermek için zaman uyumsuzdur. Uygulama bir Blazor webassembly uygulaması ise ve bir JavaScript işlevini eşzamanlı olarak çağırmak istiyorsanız bunun yerine alt türe çevirme yapın `IJSInProcessRuntime` ve çağırın `Invoke<T>` . Çoğu JS birlikte çalışma kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olmasını sağlamak için zaman uyumsuz API 'Leri kullanmasını öneririz.

Örnek uygulama, JS birlikte çalışabilirliği göstermek için bir bileşeni içerir. Bileşen:

* Bir JavaScript istemi aracılığıyla Kullanıcı girişini alır.
* İşlemek için bileşene metni döndürür.
* Bir hoş geldiniz iletisini göstermek için DOM ile etkileşime sahip ikinci bir JavaScript işlevini çağırır.

*Pages/Jsınterop. Razor*:

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

1. `TriggerJsPrompt` Bileşenin **tetikleme JavaScript istem** düğmesi seçilerek çalıştırıldığında, `showPrompt` *Wwwroot/examplejsınterop. js* dosyasında verilen JavaScript işlevi çağırılır.
1. `showPrompt` IşLEVI, HTML kodlu ve bileşene döndürülen kullanıcı girişini (kullanıcının adı) kabul eder. Bileşen, kullanıcının adını yerel bir değişkende depolar `name`.
1. İçinde `name` depolanan dize, hoş geldiniz iletisini bir başlık etiketine Işleyen bir JavaScript işlevine `displayWelcome`iletilen bir hoş geldiniz iletisine eklenmiştir.

## <a name="call-a-void-javascript-function"></a>Void JavaScript işlevini çağırın

[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren JavaScript işlevleri ile `IJSRuntime.InvokeVoidAsync`çağırılır.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Blazor Sunucu uygulamasının ne zaman prerendering olduğunu Algıla
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Öğelere başvuruları yakala

Bazı JS birlikte çalışma senaryoları HTML öğelerine başvurular gerektirir. Örneğin, bir kullanıcı arabirimi kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya `focus` ya `play`da gibi bir öğe üzerinde komut benzeri API 'ler çağırmanız gerekebilir.

Aşağıdaki yaklaşımı kullanarak bir bileşen içindeki HTML öğelerine başvuruları yakalayın:

* HTML öğesine `@ref` bir öznitelik ekleyin.
* Adı `@ref` özniteliğin değeriyle eşleşen bir `ElementReference` tür alanı tanımlayın.

Aşağıdaki örnek, `username` `<input>` öğesine bir başvuru yakalama göstermektedir:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Yalnızca ile Blazoretkileşimde bulunmayan boş bir öğenin içeriğini bulunmamalıdır için bir öğe başvurusu kullanın. Bu senaryo, bir 3. taraf API 'SI öğeye içerik sağladığı zaman yararlıdır. Öğesiyle Blazor etkileşmediği için, öğe ve Dom gösterimi arasında Blazorbir çakışma olabilir.
>
> Aşağıdaki örnekte, bu öğenin liste öğelerini (`<li>`) doldurmak üzere Dom ile etkileşimde bulunduğundan, sıralanmamış listenin`ul`() Blazor içeriğini zaman içinde () () zaman *zaman aşmaktır* .
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
> JS birlikte çalışma öğesi içeriğini değiştiriyorsa `MyList` ve Blazor öğe için SLA 'lar uygulamaya ÇALıŞıRSA, diffler Dom ile eşleşmez.

.NET kodu açısından düşünüldüğünde, donuk bir `ElementReference` tanıtıcıdır. İle *only* `ElementReference` YAPABILECEĞINIZ tek şey, JS birlikte çalışma yoluyla JavaScript koduna geçiş yapar. Bunu yaptığınızda, JavaScript tarafı kodu normal DOM API 'Leri ile kullanılabilecek `HTMLElement` bir örnek alır.

Örneğin, aşağıdaki kod bir öğe üzerinde odağı ayarlamaya izin veren bir .NET genişletme yöntemi tanımlar:

*Examplejsınterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Değer döndürmeyen bir JavaScript işlevini çağırmak için kullanın `IJSRuntime.InvokeVoidAsync`. Aşağıdaki kod, yakalanmış `ElementReference`olan önceki JavaScript işlevini çağırarak Kullanıcı adı girişi üzerinde odağı ayarlar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Bir genişletme yöntemi kullanmak için, `IJSRuntime` örneği alan bir statik genişletme yöntemi oluşturun:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` Yöntemi doğrudan nesnesi üzerinde çağrılır. Aşağıdaki örnek, `Focus` yöntemin `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` Değişken yalnızca bileşen işlendikten sonra doldurulur. Doldurulmamış bir `ElementReference` JavaScript koduna geçirilirse JavaScript kodu bir değeri alır `null`. Bileşen işlemeyi tamamladıktan sonra öğe başvurularını değiştirmek için (bir öğe üzerinde ilk odağı ayarlamak için) [Onafterrenderasync veya OnAfterRender bileşen yaşam döngüsü yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.

Genel türlerle çalışırken ve bir değer döndürürken, [Valuetask\<T>](xref:System.Threading.Tasks.ValueTask`1)kullanın:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`, bir türü olan doğrudan nesnesi üzerinde çağrılır. Aşağıdaki örnek, `GenericMethod` `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Bileşenler arasında başvuru öğeleri

Yalnızca `ElementReference` bir bileşen `OnAfterRender` yönteminde (ve bir öğe başvurusu bir `struct`) garanti edilir, bu nedenle bileşenler arasında bir öğe başvurusu geçirilememelidir.

Bir üst bileşen için bir öğe başvurusunu diğer bileşenlere kullanılabilir hale getirmek için, üst bileşen şunları yapabilir:

* Alt bileşenlerin geri çağırmaları kaydetmesine izin ver.
* Geçirilen öğe başvurusuyla `OnAfterRender` olay sırasında kayıtlı geri çağırmaları çağırın. Bu yaklaşım dolaylı olarak, alt bileşenlerin üst öğenin öğe başvurusuyla etkileşime geçmesini sağlar.

Aşağıdaki Blazor webassembly örneğinde yaklaşım gösterilmektedir.

`<head>` *Wwwroot/index.html*' de:

```html
<style>
    .red { color: red }
</style>
```

`<body>` *Wwwroot/index.html*' de:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index. Razor* (üst bileşen):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Sayfa/dizin. Razor. cs*:

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

*Shared/gözetimi ve Prompt. Razor* (alt bileşen):

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

*Shared/gözetimi ve Prompt. Razor. cs*:

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

## <a name="harden-js-interop-calls"></a>Harden JS birlikte çalışma çağrıları

JS birlikte çalışması, ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak değerlendirilmelidir. Varsayılan olarak, bir Blazor sunucu uygulaması, bir dakika sonra sunucu üzerinde BIR kez js birlikte çalışabilirlik çağrısı çağırır. Bir uygulama, 10 saniye gibi daha agresif zaman aşımına uğrayedebilmesine, aşağıdaki yaklaşımlardan birini kullanarak zaman aşımını ayarlayın:

* İçinde `Startup.ConfigureServices`genel olarak, zaman aşımını belirtin:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Bileşen kodunda çağrı başına, tek bir çağrı zaman aşımını belirtebilir:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Kaynak tükenmesi hakkında daha fazla bilgi için bkz <xref:security/blazor/server/threat-mitigation>..

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Döngüsel nesne başvurularından kaçının

Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:

* .NET yöntemi çağrıları.
* Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.

Daha fazla bilgi için aşağıdaki konulara bakın:

* [Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor Sunucu uygulamalarında büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
