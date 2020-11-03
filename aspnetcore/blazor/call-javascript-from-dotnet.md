---
title: ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor
author: guardrex
description: Uygulamalarda .NET metotlarından JavaScript işlevlerini çağırmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 17d6087b884775a8bfcb41fe23296f508467e924
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234458"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırın Blazor

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)ve [Luke Latham](https://github.com/guardrex)

Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir. Bu senaryolar *JavaScript birlikte çalışabilirliği* ( *js birlikte çalışma* ) olarak adlandırılır.

Bu makalede, .NET 'ten JavaScript işlevlerini çağırma ele alınmaktadır. JavaScript 'ten .NET yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:blazor/call-dotnet-from-javascript> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

.NET 'ten JavaScript 'e çağrı yapmak için <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı kullanın. JS birlikte çalışma çağrıları vermek için, bu <xref:Microsoft.JSInterop.IJSRuntime> soyutlamayı bileşeninizdeki ekler. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> herhangi bir sayıda JSON seri hale getirilebilir bağımsız değişkenle birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır. İşlev tanımlayıcısı genel kapsama ( `window` ) göredir. Çağırmak isterseniz `window.someScope.someFunction` , tanımlayıcı olur `someScope.someFunction` . Çağrılmadan önce işlevi kaydetmeniz gerekmez. Dönüş türünün `T` de JSON seri hale getirilebilir olması gerekir. `T` döndürülen JSON türüyle en iyi eşleşen .NET türüyle eşleşmelidir.

[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) döndüren JavaScript işlevleri ile çağırılır <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> . `InvokeAsync` Taahhüdünü geri sarar ve Promise tarafından beklenen değeri döndürür.

Blazor ServerPrerendering etkin olan uygulamalar için, ilk prerendering sırasında JavaScript 'e çağrı yapılamaz. JavaScript birlikte çalışma çağrılarının, tarayıcıyla bağlantı kurulana kadar ertelenmesi gerekir. Daha fazla bilgi için bkz. [bir uygulamanın ne zaman Blazor Server prerendering](#detect-when-a-blazor-server-app-is-prerendering) bölümüne bakın.

Aşağıdaki örnek [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , JavaScript tabanlı bir kod çözücüsünü temel alır. Örnek, geliştirici koddan mevcut bir JavaScript API 'sine bir gereksinimi boşaltan bir C# yönteminden JavaScript işlevinin nasıl çağrılacağını gösterir. JavaScript işlevi bir C# yönteminden bir bayt dizisi kabul eder, dizinin kodunu çözer ve görüntülenecek metni bileşene döndürür.

`<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server , geçirilen bir dizinin kodunu çözmek için kullanılan bir JavaScript işlevi sağlayın `TextDecoder` ve kodu çözülen değeri döndürün:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Önceki örnekte gösterilen kod gibi JavaScript kodu, `.js` betik dosyasına yönelik bir başvuruya sahip bir JavaScript dosyasından () de yüklenebilir:

```html
<script src="exampleJsInterop.js"></script>
```

Aşağıdaki bileşen:

* `convertArray` `JS` Bir bileşen düğmesi () seçildiğinde JavaScript işlevini çağırır **`Convert Array`** .
* JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dizeye dönüştürülür. Dize, görüntüleme için bileşene döndürülür.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

<xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:

* <xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı Razor bileşene ekleyin ( `.razor` ):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  `<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar. İşlevi ile çağrılır <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ve bir değer döndürmez:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <xref:Microsoft.JSInterop.IJSRuntime>Soyutlamayı bir sınıfa () Ekle `.cs` :

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  `<head>` `wwwroot/index.html` ( Blazor WebAssembly ) Veya () öğesinin içinde `Pages/_Host.cshtml` Blazor Server bir `handleTickerChanged` JavaScript işlevi sağlar. İşlevi ile çağrılır `JS.InvokeAsync` ve bir değer döndürür:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [Buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma için `[Inject]` özniteliğini kullanın:

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

Bu konuya eşlik eden istemci tarafı örnek uygulamada, Kullanıcı girişi almak ve bir hoş geldiniz iletisi göstermek üzere DOM ile etkileşime geçen uygulama için iki JavaScript işlevi mevcuttur:

* `showPrompt`: Kullanıcı girişini kabul etmek için bir istem üretir (kullanıcının adı) ve arayan adına adı döndürür.
* `displayWelcome`: Çağıran bir DOM nesnesine çağırandan bir hoş geldiniz iletisi atar `id` `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

`<script>` `wwwroot/index.html` Dosyadaki ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyadaki () JavaScript dosyasına başvuran etiketi yerleştirin Blazor Server .

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

`<script>` `<script>` Etiket dinamik olarak güncelleştirilemediğinden bir bileşen dosyasına etiket yerleştirmeyin.

.NET yöntemleri, çağırarak dosyadaki JavaScript işlevleriyle birlikte çalışır `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

<xref:Microsoft.JSInterop.IJSRuntime>Soyutlama, senaryolara izin vermek için zaman uyumsuzdur Blazor Server . Uygulama bir Blazor WebAssembly uygulama ise ve bir JavaScript işlevini zaman uyumlu olarak çağırmak istiyorsanız, bunun yerine alt türe çevirme yapın <xref:Microsoft.JSInterop.IJSInProcessRuntime> ve çağırın <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> . Çoğu JS birlikte çalışma kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olmasını sağlamak için zaman uyumsuz API 'Leri kullanmasını öneririz.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımını etkinleştirmek için [ Blazor JavaScript yalıtım ve nesne başvuruları](#blazor-javascript-isolation-and-object-references) bölümüne bakın.

::: moniker-end

Örnek uygulama, JS birlikte çalışabilirliği göstermek için bir bileşeni içerir. Bileşen:

* Bir JavaScript istemi aracılığıyla Kullanıcı girişini alır.
* İşlemek için bileşene metni döndürür.
* Bir hoş geldiniz iletisini göstermek için DOM ile etkileşime sahip ikinci bir JavaScript işlevini çağırır.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

1. `TriggerJsPrompt`Bileşen düğme seçilerek yürütüldüğünde **`Trigger JavaScript Prompt`** , `showPrompt` dosyada verilen JavaScript işlevi `wwwroot/exampleJsInterop.js` çağırılır.
1. `showPrompt`İşlevi, HTML kodlu ve bileşene döndürülen kullanıcı girişini (kullanıcının adı) kabul eder. Bileşen, kullanıcının adını yerel bir değişkende depolar `name` .
1. İçinde depolanan dize, `name` `displayWelcome` hoş geldiniz iletisini bir başlık etiketine Işleyen bir JavaScript işlevine iletilen bir hoş geldiniz iletisine eklenmiştir.

## <a name="call-a-void-javascript-function"></a>Void JavaScript işlevini çağırın

<xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>Aşağıdakiler için kullanın:

* [Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [tanımsız](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)döndüren JavaScript işlevleri.
* Bir JavaScript çağrısının sonucunu okumak için .NET gerekmiyorsa.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Bir uygulamanın ne zaman Blazor Server prerendering olduğunu Algıla
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Öğelere başvuruları yakala

Bazı JS birlikte çalışma senaryoları HTML öğelerine başvurular gerektirir. Örneğin, bir kullanıcı arabirimi kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya ya da gibi bir öğe üzerinde komut benzeri API 'Ler çağırmanız gerekebilir `focus` `play` .

Aşağıdaki yaklaşımı kullanarak bir bileşen içindeki HTML öğelerine başvuruları yakalayın:

* `@ref`HTML öğesine bir öznitelik ekleyin.
* <xref:Microsoft.AspNetCore.Components.ElementReference>Adı özniteliğin değeriyle eşleşen bir tür alanı tanımlayın `@ref` .

Aşağıdaki örnek, öğesine bir başvuru yakalama göstermektedir `username` `<input>` :

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Yalnızca ile etkileşimde bulunmayan boş bir öğenin içeriğini bulunmamalıdır için bir öğe başvurusu kullanın Blazor . Bu senaryo, üçüncü taraf bir API 'nin öğeye içerik sağladığı durumlarda faydalıdır. BlazorÖğesiyle etkileşmediği için, Blazor öğe ve Dom gösterimi arasında bir çakışma olabilir.
>
> Aşağıdaki örnekte, *dangerous* `ul` Blazor Bu öğenin liste öğelerini () doldurmak üzere Dom ile etkileşimde bulunduğundan, sıralanmamış listenin () içeriğini zaman içinde () () zaman zaman aşmaktır `<li>` .
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
> JS birlikte çalışma öğesi içeriğini değiştiriyorsa `MyList` ve Blazor öğe için SLA 'lar uygulamaya çalışırsa, DIFFLER Dom ile eşleşmez.

<xref:Microsoft.AspNetCore.Components.ElementReference>, JS birlikte çalışması aracılığıyla JavaScript koduna geçirilir. JavaScript kodu `HTMLElement` , normal Dom API 'leri ile kullanılabilecek bir örnek alır. Örneğin, aşağıdaki kod bir öğeye fare tıklamasını göndermeyi sağlayan bir .NET genişletme yöntemi tanımlar:

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element)C# kodunda, Blazor çerçeveye yerleştirilmiş ve öğe başvurularıyla birlikte çalışarak bir öğeyi odaklamak için kullanın.

::: moniker-end

Değer döndürmeyen bir JavaScript işlevini çağırmak için kullanın <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Aşağıdaki kod, `Click` yakalanarak önceki JavaScript işlevini çağırarak istemci tarafı olayını tetikler <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

Bir genişletme yöntemi kullanmak için, örneği alan bir statik genişletme yöntemi oluşturun <xref:Microsoft.JSInterop.IJSRuntime> :

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

`clickElement`Yöntemi doğrudan nesnesi üzerinde çağrılır. Aşağıdaki örnek, `TriggerClickEvent` yöntemin ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> `exampleButton`Değişken yalnızca bileşen işlendikten sonra doldurulur. Doldurulmamış bir <xref:Microsoft.AspNetCore.Components.ElementReference> JavaScript koduna geçirilirse JavaScript kodu bir değeri alır `null` . Bileşen işlemeyi tamamladıktan sonra öğe başvurularını işlemek için [ `OnAfterRenderAsync` veya `OnAfterRender` bileşen yaşam döngüsü yöntemlerini](xref:blazor/components/lifecycle#after-component-render)kullanın.

Genel türlerle çalışırken ve bir değer döndürürken şunu kullanın <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` , bir türü olan doğrudan nesnesi üzerinde çağrılır. Aşağıdaki örnek, `GenericMethod` ad alanından kullanılabilir olduğunu varsayar `JsInteropClasses` :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Bileşenler arasında başvuru öğeleri

<xref:Microsoft.AspNetCore.Components.ElementReference>Şu nedenle bileşenler arasında geçirilebilir:

* Örnek yalnızca bileşen oluşturulduktan sonra, bir bileşenin yöntemi yürütüldüğünde veya bu tarihten sonra olduğu garanti edilir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .
* <xref:Microsoft.AspNetCore.Components.ElementReference> [`struct`](/csharp/language-reference/builtin-types/struct) , Bir [bileşen parametresi](xref:blazor/components/index#component-parameters)olarak geçirilememelidir.

Bir üst bileşen için bir öğe başvurusunu diğer bileşenlere kullanılabilir hale getirmek için, üst bileşen şunları yapabilir:

* Alt bileşenlerin geri çağırmaları kaydetmesine izin ver.
* Geçirilen öğe başvurusuyla olay sırasında kayıtlı geri çağırmaları çağırın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> . Bu yaklaşım dolaylı olarak, alt bileşenlerin üst öğenin öğe başvurusuyla etkileşime geçmesini sağlar.

Aşağıdaki Blazor WebAssembly örnekte yaklaşım gösterilmektedir.

İçinde `<head>` `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

İçinde `<body>` `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (üst bileşen):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

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
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

`Shared/SurveyPrompt.razor` (alt bileşen):

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

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Harden JS birlikte çalışma çağrıları

JS birlikte çalışması, ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak değerlendirilmelidir. Varsayılan olarak, bir Blazor Server uygulama bir dakika sonra sunucu üzerinde bir kez js birlikte çalışabilirlik çağrısı çağırır. Bir uygulama daha agresif zaman aşımına uğrayedebiliyorsanız, aşağıdaki yaklaşımlardan birini kullanarak zaman aşımını ayarlayın:

* İçinde genel olarak `Startup.ConfigureServices` , zaman aşımını belirtin:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Bileşen kodunda çağrı başına, tek bir çağrı zaman aşımını belirtebilir:

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Kaynak tükenmesi hakkında daha fazla bilgi için bkz <xref:blazor/security/server/threat-mitigation> ..

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Döngüsel nesne başvurularından kaçının

Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:

* .NET yöntemi çağrıları.
* Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.

Daha fazla bilgi için aşağıdaki konulara bakın:

* [Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript yalıtımı ve nesne başvuruları

Blazor Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez. JavaScript yalıtımı aşağıdaki avantajları sağlar:

* İçeri aktarılan JavaScript artık genel ad alanını pollutes.
* İlişkili JavaScript 'in içe aktarılması için bir kitaplık ve bileşenlerin tüketicileri gerekli değildir.

Örneğin, aşağıdaki JavaScript modülü bir tarayıcı istemi göstermek için bir JavaScript işlevi dışarı aktarır:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Önceki JavaScript modülünü bir .NET kitaplığına statik web varlığı () olarak ekleyin `wwwroot/exampleJsInterop.js` ve ardından hizmeti kullanarak modülü .net koduna aktarın <xref:Microsoft.JSInterop.IJSRuntime> . Hizmet, `js` Aşağıdaki örnek için (gösterilmez) olarak eklenir:

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

`import`Önceki örnekteki tanımlayıcı, özellikle bir JavaScript modülünü içeri aktarmak için kullanılan özel bir tanıtıcıdır. Sabit statik Web varlık yolunu kullanarak modülü belirtin: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . Yer tutucu, `{LIBRARY NAME}` kitaplık adıdır. Yer tutucu, `{PATH UNDER WWWROOT}` altında betiğin yoludur `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> modülünü `IJSObjectReference` .NET kodundan bir JavaScript nesnesine başvuruyu temsil eden bir olarak içeri aktarır. Modülünden, `IJSObjectReference` içe aktarılmış JavaScript işlevlerini çağırmak için kullanın:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` işlevleri zaman uyumlu olarak çağrılabilen bir JavaScript nesnesine olan başvuruyu temsil eder.

`IJSUnmarshalledObjectReference` işlevleri .NET verilerinin serileştirilmesi olmadan çağrılabilen bir JavaScript nesnesine yönelik başvuruyu temsil eder. Bu, Blazor WebAssembly performans önemli olduğunda ' de kullanılabilir:

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

Önceki örnekte, <xref:Microsoft.JSInterop.IJSRuntime> hizmet sınıfına eklenir ve şuna atanır `js` (gösterilmez).

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Kullanıcı arabirimini (DOM öğeleri) işleyen JavaScript kitaplıklarının kullanımı

Bazen, tarayıcı DOM içinde görünür kullanıcı arabirimi öğeleri üreten JavaScript kitaplıklarını kullanmak isteyebilirsiniz. İlk bakışta, bu durum, Blazor Dağıtılmış Sistem, Dom öğelerinin ağacı üzerinde denetime sahip olma ve bazı dış kodlar Dom ağacını bir kez değiştiğinden ve bunların SLA 'ları uygulama mekanizmasını geçersiz kılacağından hatalara açık hale gelebilir. Bu, Blazor belirli bir kısıtlama değildir. Aynı zorluk, fark tabanlı kullanıcı arabirimi çerçevesiyle oluşur.

Neyse ki, dışarıdan oluşturulan kullanıcı arabirimini Blazor bileşen Kullanıcı arabirimine güvenilir bir şekilde eklemek çok basittir. Önerilen yöntem, bileşenin kodunun ( `.razor` dosya) boş bir öğe üretmektir. BlazorYayılma sisteminin en çok ilgisi olduğu gibi, öğe her zaman boştur, bu nedenle işleyici öğesi için bir değer değil, onun içeriğini tek başına bırakır. Bu, öğesini rastgele dışarıdan yönetilen içerikle doldurmayı güvenli hale getirir.

Aşağıdaki örnek kavramı gösterir. İfadesinin içinde `if` olduğunda `firstRender` `true` , ile bir işlem yapın `myElement` . Örneğin, doldurmak için bir dış JavaScript kitaplığı çağırın. Blazor Bu bileşen kaldırılana kadar öğenin içeriğini tek başına bırakır. Bileşen kaldırıldığında, bileşenin tüm DOM alt ağacı da kaldırılır.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

Daha ayrıntılı bir örnek olarak, [Açık kaynaklı Mapbox API 'lerini](https://www.mapbox.com/)kullanarak etkileşimli bir eşleme oluşturan aşağıdaki bileşeni göz önünde bulundurun:

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

Kendisine yerleştirilmesi gereken ilgili JavaScript modülü `wwwroot/mapComponent.js` aşağıdaki gibidir:

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

Yukarıdaki örnekte, dizeyi, `{ACCESS TOKEN}` alacağınız geçerli bir erişim belirteciyle değiştirin https://account.mapbox.com .

Doğru stil oluşturmak için, ana bilgisayar HTML sayfasına aşağıdaki stil sayfası etiketini ekleyin ( `index.html` veya `_Host.cshtml` ):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

Yukarıdaki örnek, kullanıcının şu şekilde bir etkileşimli harita Kullanıcı arabirimi üretir:

* , Kaydırmak veya yakınlaştırmak için sürükleyebilir.
* Önceden tanımlanmış konumlara geçmek için düğmeler ' e tıklayın.

![Esistol, Birleşik Krallık ve Tokyo, Japonya ' ı seçmek için bkz. Tokyo 'daki mapbox açık Haritası](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Anlaşılması için önemli noktaları şunlardır:

 * , `<div>` İle ilişkili olduğu kadar `@ref="mapElement"` boş bırakılır Blazor . Bu nedenle `mapbox-gl.js` , bunu doldurmak ve içeriğini zaman içinde değiştirmek güvenlidir. Bu tekniği, Kullanıcı arabirimini işleyen herhangi bir JavaScript kitaplığıyla kullanabilirsiniz. Ayrıca, bileşen içindeki bir üçüncü taraf JavaScript SPA çerçevesinin Blazor , sayfanın diğer bölümlerine ulaşmaya ve bunları değiştirmeye çalıştıklarında bile bileşenleri ekleyebilirsiniz. Dış JavaScript kodu için boş olarak söz konusu olmayan öğeleri değiştirme güvenli *değildir* Blazor .
 * Bu yaklaşımı kullanırken, Blazor Dom öğelerinin nasıl koruduğunu veya yok olduğunu gösteren kuralları göz önünde bulundurun. Önceki örnekte, bileşen düğme tıklama olayları ' nı güvenli bir şekilde işler ve DOM öğeleri varsayılan olarak mümkün olduğunda korunduğu için mevcut eşleme örneğini güncelleştirir. Bir döngünün içindeki harita öğelerinin bir listesini işlerinizden `@foreach` , `@key` bileşen örneklerinin korunmasını sağlamak için kullanmak istersiniz. Aksi takdirde, liste verilerinde yapılan değişiklikler bileşen örneklerinin önceki örneklerin durumunu istenmeyen bir şekilde tutmasına neden olabilir. Daha fazla bilgi için bkz [. @key öğeleri ve bileşenleri korumak için kullanma](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Ayrıca, önceki örnekte, JavaScript mantığını ve bağımlılıklarını bir ES6 modülü içinde kapsüllemek ve tanımlayıcıyı kullanarak dinamik olarak yüklemek nasıl mümkün olduğunu gösterir `import` . Daha fazla bilgi için bkz. [JavaScript yalıtım ve nesne başvuruları](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>JS birlikte çalışma çağrılarında boyut sınırları

' De Blazor WebAssembly , çerçeve, JS birlikte çalışma çağrılarının giriş ve çıkışları için sınır vermez.

Blazor Server' De, BIR js birlikte çalışma çağrısının sonucu, varsayılan olarak SignalR <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 32 KB olan () tarafından zorlanan en fazla yük boyutu ile sınırlıdır. Bir hatayla daha büyük bir yük ile bir JS birlikte çalışma çağrısına yanıt vermeye çalışacak uygulamalar <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . Daha büyük bir sınır, değiştirilerek yapılandırılabilir <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . Aşağıdaki örnek, en fazla alma iletisi boyutunu 64 KB (64 * 1024 * 1024) olarak ayarlar:

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

Sınırın artırılması SignalR , daha fazla sunucu kaynağı kullanımını isteme maliyetine gelir ve sunucuyu kötü amaçlı bir kullanıcıdan daha fazla risk artışı için kullanıma sunar. Ayrıca, dizeler veya bayt dizileri olarak bellekte büyük miktarda içeriği okumak, çöp toplayıcıyla kötü olarak çalışan ve ek performans cezaları elde eden ayırmaya neden olabilir. Büyük yükleri okumak için bir seçenek, içeriğin daha küçük parçalara gönderilmesini ve yükün bir olarak işlenmesini düşünmelidir <xref:System.IO.Stream> . Bu, büyük JSON yüklerini okurken veya veriler JavaScript 'te ham bayt olarak kullanılabilir olduğunda kullanılabilir. İçinde, bileşene benzer teknikleri kullanan büyük ikili yükleri göndermeyi gösteren bir örnek için Blazor Server `InputFile` bkz. [ikili gönderme örnek uygulaması](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

JavaScript arasında büyük miktarda veri aktaran kodu geliştirirken aşağıdaki kılavuzu göz önünde bulundurun Blazor :

* Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri segmentlerini sırayla gönderin.
* JavaScript ve C# kodunda büyük nesneler ayırmayın.
* Veri gönderirken veya alırken uzun süreler için ana UI iş parçacığını engellemez.
* İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm belleği boşaltın.
* Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:
  * Geçirilebilen en büyük dosya veya veri boyutunu bildirin.
  * İstemciden sunucuya en düşük karşıya yükleme hızını bildirin.
* Veriler, sunucu tarafından alındıktan sonra şu olabilir:
  * Tüm segmentler toplanana kadar bir bellek arabelleğinde geçici olarak depolanır.
  * Hemen tüketildi. Örneğin, veriler bir veritabanında hemen depolanabilir veya her bir segment alındığında diske yazılabilir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
