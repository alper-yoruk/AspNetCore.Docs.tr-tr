---
title: ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırın Blazor
author: guardrex
description: Uygulamalarda JavaScript işlevlerinden .NET yöntemlerini çağırmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 4a7c06ed985c290eb4b3ffca6d5ed74c6bc4e031
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690296"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırın Blazor

, [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex)

Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir. Bu senaryolar *JavaScript birlikte çalışabilirliği* ( *js birlikte çalışma* ) olarak adlandırılır.

Bu makalede, JavaScript 'ten .NET yöntemlerini çağırma ele alınmaktadır. .NET JavaScript işlevlerinin nasıl çağrılacağını öğrenmek için bkz <xref:blazor/call-javascript-from-dotnet> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statik .NET yöntemi çağrısı

JavaScript 'ten statik bir .NET yöntemi çağırmak için `DotNet.invokeMethod` veya `DotNet.invokeMethodAsync` işlevlerini kullanın. Çağırmak istediğiniz statik metodun tanımlayıcısını, işlevi içeren derlemenin adını ve tüm bağımsız değişkenleri geçirin. Zaman uyumsuz sürüm, senaryoları desteklemek için tercih edilir Blazor Server . .NET yöntemi genel, statik ve [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) özniteliği olmalıdır. Açık genel yöntemlerin çağrılması Şu anda desteklenmiyor.

Örnek uygulama, bir dizi döndürmek için C# yöntemi içerir `int` . [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)Özniteliği yöntemine uygulanır.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

İstemciye sunulan JavaScript, C# .NET yöntemini çağırır.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**`Trigger .NET static method ReturnArrayAsync`** Düğme seçildiğinde, tarayıcının Web geliştirici araçlarında konsol çıkışını inceleyin.

Konsol çıktısı:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Dördüncü dizi değeri `data.push(4);` tarafından döndürülen diziye () gönderilir `ReturnArrayAsync` .

Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak öznitelik oluşturucusunu kullanarak farklı bir tanımlayıcı belirtebilirsiniz [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) :

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

İstemci tarafı JavaScript dosyasında:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

## <a name="instance-method-call"></a>Örnek yöntem çağrısı

JavaScript 'ten de .NET örnek yöntemlerini çağırabilirsiniz. JavaScript 'ten bir .NET örnek yöntemi çağırmak için:

* .NET örneğini JavaScript 'e başvuruya göre geçirin:
  * İçin statik bir çağrı yapın <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .
  * Örneği bir örnek içinde sarın <xref:Microsoft.JSInterop.DotNetObjectReference> ve <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> örnek üzerinde çağırın <xref:Microsoft.JSInterop.DotNetObjectReference> . Nesneleri atma <xref:Microsoft.JSInterop.DotNetObjectReference> (Bu bölümün ilerleyen kısımlarında bir örnek görünür).
* Or işlevlerini kullanarak örnekte .NET örnek yöntemlerini çağırın `invokeMethod` `invokeMethodAsync` . .NET örneği, JavaScript 'ten başka .NET yöntemleri çağrılırken bir bağımsız değişken olarak da geçirilebilir.

> [!NOTE]
> Örnek uygulama, iletileri istemci tarafı konsoluna kaydeder. Örnek uygulama tarafından gösterilen aşağıdaki örnekler için tarayıcının geliştirici araçlarında tarayıcının konsol çıkışını inceleyin.

**`Trigger .NET instance method HelloHelper.SayHello`** Düğme seçildiğinde, `ExampleJsInterop.CallHelloHelperSayHello` çağrılır ve yöntemine bir ad geçirir `Blazor` .

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` JavaScript işlevini `sayHello` Yeni bir örneğiyle çağırır `HelloHelper` .

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Ad, `HelloHelper` özelliğini ayarlayan oluşturucuya geçirilir `HelloHelper.Name` . JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` `Hello, {Name}!` JavaScript işlevi tarafından konsola yazılan iletiyi döndürür.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Tarayıcının Web geliştirici araçlarında konsol çıkışı:

```console
Hello, Blazor!
```

Bir bellek sızıntısını önlemek ve oluşturan bir bileşende çöp toplamaya izin vermek için <xref:Microsoft.JSInterop.DotNetObjectReference> aşağıdaki yaklaşımlardan birini benimseyin:

* Örneği oluşturan sınıftaki nesneyi atma <xref:Microsoft.JSInterop.DotNetObjectReference> :

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  Sınıfında gösterilen önceki model `ExampleJsInterop` de bir bileşende uygulanabilir:

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

* Bileşen veya sınıf öğesini atmazsa <xref:Microsoft.JSInterop.DotNetObjectReference> , şunu çağırarak istemci üzerindeki nesnesini atın `.dispose()` :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Bileşen örneği Yöntem çağrısı

Bir bileşenin .NET yöntemlerini çağırmak için:

* `invokeMethod` `invokeMethodAsync` Bileşene statik yöntem çağrısı yapmak için or işlevini kullanın.
* Bileşenin static yöntemi, çağırma yöntemi olarak çağrılır <xref:System.Action> .

> [!NOTE]
> Blazor ServerBirden çok kullanıcının aynı bileşeni aynı anda kullandığı uygulamalarda, örnek yöntemleri çağırmak için bir yardımcı sınıfı kullanın.
>
> Daha fazla bilgi için bkz. [bileşen örnek yöntemi yardımcı sınıfı](#component-instance-method-helper-class) bölümü.

İstemci tarafı JavaScript 'te:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Bağımsız değişkenleri örnek yöntemine geçirmek için:

* JS yöntem çağrısına parametreler ekleyin. Aşağıdaki örnekte yöntemine bir ad geçirilir. Gerektiğinde, ek parametreler listeye eklenebilir.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

* Parametreleri için doğru türleri sağlayın <xref:System.Action> . C# yöntemlerine parametre listesini sağlayın. <xref:System.Action>() Parametreleriyle () öğesini çağırın `UpdateMessage` `action.Invoke(name)` .

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  `message` **Çağıran js yöntemi** düğmesi seçildiğinde çıkış:

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Bileşen örnek yöntemi yardımcı sınıfı

Yardımcı sınıfı bir örnek yöntemini bir olarak çağırmak için kullanılır <xref:System.Action> . Yardımcı sınıflar şu durumlarda yararlı olur:

* Aynı türdeki birçok bileşen aynı sayfada işlenir.
* Blazor ServerBirden çok kullanıcının aynı anda bir bileşen kullandığı bir uygulama kullanılır.

Aşağıdaki örnekte:

* `JSInteropExample`Bileşen birkaç bileşen içerir `ListItem` .
* Her `ListItem` bileşen bir ileti ve bir düğmeden oluşur.
* Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem` `UpdateMessage` Yöntem liste öğesi metnini değiştirir ve düğmeyi gizler.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

İstemci tarafı JavaScript 'te:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Döngüsel nesne başvurularından kaçının

Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:

* .NET yöntemi çağrıları.
* Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.

Daha fazla bilgi için aşağıdaki konulara bakın:

* [Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` örnek (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Uygulamalarda büyük veri aktarımları gerçekleştirme Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
