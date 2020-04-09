---
title: ASP.NET Core'daki JavaScript işlevlerinden .NET yöntemlerini arayınBlazor
author: guardrex
description: Uygulamalardaki Blazor JavaScript işlevlerinden .NET yöntemlerini nasıl çağırılamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976956"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>ASP.NET Core'daki JavaScript işlevlerinden .NET yöntemlerini arayınBlazor

Javier [Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bir Blazor uygulama JavaScript işlevlerinden .NET yöntemlerinden ve .NET yöntemlerinden JavaScript işlevlerini çağırabilir. Bu senaryolar *JavaScript birlikte çalışabilirlik* *(JS interop)* olarak adlandırılır.

Bu makale, JavaScript'ten .NET yöntemlerini çağrıştıran kapsar. .NET'ten JavaScript işlevlerini nasıl arayacağız hakkında bilgi için bkz. <xref:blazor/call-javascript-from-dotnet>

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statik .NET yöntemi çağrısı

JavaScript'ten statik bir .NET yöntemi `DotNet.invokeMethod` `DotNet.invokeMethodAsync` çağırmak için, işlevleri kullanın. Çağırmak istediğiniz statik yöntemin tanımlayıcısını, işlevi içeren derlemenin adını ve bağımsız değişkenleri geçirin. Asynchronous sürümü Sunucu senaryoları desteklemek Blazor için tercih edilir. .NET yöntemi genel, statik olmalı ve `[JSInvokable]` özniteliğe sahip olmalıdır. Açık genel yöntemleri çağırmak şu anda desteklenmiyor.

Örnek uygulama, bir `int` diziyi döndürmek için bir C# yöntemi içerir. Öznitelik `JSInvokable` yönteme uygulanır.

*Sayfa/JsInterop.razor*:

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

İstemciye sunulan JavaScript C# .NET yöntemini çağırır.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Trigger **.NET statik yöntem ReturnArrayAsync** düğmesi seçildiğinde, konsol çıktısını tarayıcının web geliştirici araçlarında inceleyin.

Konsol çıkışı:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Dördüncü dizi değeri diziye itilir`data.push(4);`( `ReturnArrayAsync`) tarafından döndürülür.

Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak `JSInvokableAttribute` oluşturucuyu kullanarak farklı bir tanımlayıcı belirtebilirsiniz:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

İstemci tarafındaki JavaScript dosyasında:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Örnek yöntem çağrısı

JavaScript'ten .NET örnek yöntemlerini de arayabilirsiniz. JavaScript'ten bir .NET örnek yöntemi çağırmak için:

* .NET örneğini JavaScript'e atıfta bulunarak geçirin:
  * Statik arama `DotNetObjectReference.Create`yapın.
  * Örneği bir `DotNetObjectReference` örnekte sarın `DotNetObjectReference` ve örneği çağırın. `Create` `DotNetObjectReference` Nesneleri atın (bu bölümde daha sonra bir örnek görüntülenir).
* Instance veya işlevleri kullanarak .NET `invokeMethod` `invokeMethodAsync` örnek yöntemlerini çağırın. .NET örneği, JavaScript'ten diğer .NET yöntemlerini çağırırken bir bağımsız değişken olarak da geçirilebilir.

> [!NOTE]
> Örnek uygulama iletileri istemci tarafındaki konsola kaydeder. Örnek uygulama tarafından gösterilen aşağıdaki örnekler için, tarayıcının konsol çıktısını tarayıcının geliştirici araçlarında inceleyin.

Trigger **.NET örnek yöntemi HelloHelper.SayHello** düğmesi `ExampleJsInterop.CallHelloHelperSayHello` seçildiğinde, çağrılır ve yönteme bir ad `Blazor`geçer.

*Sayfa/JsInterop.razor*:

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

`CallHelloHelperSayHello`yeni bir örneği `sayHello` ile JavaScript `HelloHelper`işlevini çağırır.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Ad, özelliği `HelloHelper`belirleyen ''ın yapıcısına `HelloHelper.Name` geçer. JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` `Hello, {Name}!` JavaScript işlevi tarafından konsola yazılan iletiyi döndürür.

*JsInteropClasses/ HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Tarayıcının web geliştirici araçlarında konsol çıktısı:

```console
Hello, Blazor!
```

Bellek sızıntısını önlemek ve aşağıdaki yaklaşımlardan birini `DotNetObjectReference`benimseyen bir bileşenüzerinde çöp toplamaya izin vermek için:

* Örneği oluşturan sınıftaki nesneyi `DotNetObjectReference` atın:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  `ExampleJsInterop` Sınıfta gösterilen önceki desen bir bileşende de uygulanabilir:

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* Bileşen veya sınıf atmadığı `DotNetObjectReference`zaman, nesneyi istemciye atarak `.dispose()`atın:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Bileşen örneği yöntemi çağrısı

Bir bileşenin .NET yöntemlerini çağırmak için:

* Bileşene `invokeMethod` `invokeMethodAsync` statik bir yöntem çağrısı yapmak için veya işlevi kullanın.
* Bileşenin statik yöntemi çağrıyı örnek yöntemine çağrıolarak `Action`sarar.

İstemci tarafındaki JavaScript'te:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Sayfa/JSInteropComponent.razor*:

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

Her biri çağrılması gereken örnek yöntemleri olan birkaç bileşen olduğunda, her `Action`bileşenin örnek yöntemlerini (as s) çağırmak için bir yardımcı sınıf kullanın.

Aşağıdaki örnekte:

* Bileşen `JSInterop` birkaç `ListItem` bileşen içerir.
* Her `ListItem` bileşen bir ileti ve düğmeden oluşur.
* Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem`''nin `UpdateMessage` yöntemi liste öğesi metnini değiştirir ve düğmeyi gizler.

*MessageUpdateInvokeHelper.cs*:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

İstemci tarafındaki JavaScript'te:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Paylaşılan/ListItem.razor*:

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

*Sayfa/JSInterop.razor*:

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Dairesel nesne başvurularından kaçının

Dairesel başvurular içeren nesneler istemcide aşağıdakiler için serileştirilemez:

* .NET yöntemi çağırır.
* İade türü dairesel referansları olduğunda JavaScript yöntemi C#'dan çağrı verir.

Daha fazla bilgi için aşağıdaki sorunlara bakın:

* [Dairesel başvurular desteklenmez, iki tane alın (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Öneri: Serileştirme yaparken dairesel başvuruları işlemek için mekanizma ekleme (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor örnek (dotnet/AspNetCore GitHub deposu, 3.1 sürüm şubesi)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
