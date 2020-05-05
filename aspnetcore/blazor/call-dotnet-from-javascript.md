---
title: ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırınBlazor
author: guardrex
description: Blazor Uygulamalarda JavaScript işlevlerinden .net yöntemlerini çağırmayı öğrenin.
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1bc75f0825b114a24def287bb7ccb11c27514f01
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767193"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="ae85a-103">ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırınBlazor</span><span class="sxs-lookup"><span data-stu-id="ae85a-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="ae85a-104">, [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ae85a-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ae85a-105">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="ae85a-106">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ae85a-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="ae85a-107">Bu makalede, JavaScript 'ten .NET yöntemlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ae85a-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="ae85a-108">.NET JavaScript işlevlerinin nasıl çağrılacağını öğrenmek için bkz <xref:blazor/call-javascript-from-dotnet>..</span><span class="sxs-lookup"><span data-stu-id="ae85a-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="ae85a-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ae85a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="ae85a-110">Statik .NET yöntemi çağrısı</span><span class="sxs-lookup"><span data-stu-id="ae85a-110">Static .NET method call</span></span>

<span data-ttu-id="ae85a-111">JavaScript 'ten statik bir .NET yöntemi çağırmak için `DotNet.invokeMethod` veya `DotNet.invokeMethodAsync` işlevlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="ae85a-112">Çağırmak istediğiniz statik metodun tanımlayıcısını, işlevi içeren derlemenin adını ve tüm bağımsız değişkenleri geçirin.</span><span class="sxs-lookup"><span data-stu-id="ae85a-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="ae85a-113">Sunucu senaryolarını desteklemek Blazor için zaman uyumsuz sürüm tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="ae85a-114">.NET yöntemi genel, statik ve `[JSInvokable]` özniteliği olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ae85a-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="ae85a-115">Açık genel yöntemlerin çağrılması Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="ae85a-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="ae85a-116">Örnek uygulama, bir `int` dizi döndürmek Için C# yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="ae85a-117">`JSInvokable` Özniteliği yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ae85a-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="ae85a-118">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="ae85a-119">İstemciye sunulan JavaScript, C# .NET yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="ae85a-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="ae85a-120">*Wwwroot/Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="ae85a-121">**Tetikleyici .net static yöntemi ReturnArrayAsync** düğmesi seçildiğinde, tarayıcının Web geliştirici araçlarında konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ae85a-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="ae85a-122">Konsol çıktısı:</span><span class="sxs-lookup"><span data-stu-id="ae85a-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="ae85a-123">Dördüncü dizi değeri tarafından`data.push(4);` `ReturnArrayAsync`döndürülen diziye () gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="ae85a-124">Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak `JSInvokableAttribute` oluşturucuyu kullanarak farklı bir tanımlayıcı belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ae85a-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="ae85a-125">İstemci tarafı JavaScript dosyasında:</span><span class="sxs-lookup"><span data-stu-id="ae85a-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="ae85a-126">Örnek yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="ae85a-126">Instance method call</span></span>

<span data-ttu-id="ae85a-127">JavaScript 'ten de .NET örnek yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ae85a-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="ae85a-128">JavaScript 'ten bir .NET örnek yöntemi çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="ae85a-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="ae85a-129">.NET örneğini JavaScript 'e başvuruya göre geçirin:</span><span class="sxs-lookup"><span data-stu-id="ae85a-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="ae85a-130">İçin `DotNetObjectReference.Create`statik bir çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="ae85a-131">Örneği bir `DotNetObjectReference` örnek içinde sarın ve `Create` `DotNetObjectReference` örnek üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="ae85a-132">`DotNetObjectReference` Nesneleri atma (Bu bölümün ilerleyen kısımlarında bir örnek görünür).</span><span class="sxs-lookup"><span data-stu-id="ae85a-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="ae85a-133">`invokeMethod` Or `invokeMethodAsync` işlevlerini kullanarak örnekte .NET örnek yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="ae85a-134">.NET örneği, JavaScript 'ten başka .NET yöntemleri çağrılırken bir bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="ae85a-135">Örnek uygulama, iletileri istemci tarafı konsoluna kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ae85a-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="ae85a-136">Örnek uygulama tarafından gösterilen aşağıdaki örnekler için tarayıcının geliştirici araçlarında tarayıcının konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ae85a-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="ae85a-137">**Tetikleyici .NET örnek yöntemi HelloHelper. SayHello** düğmesi seçildiğinde, `ExampleJsInterop.CallHelloHelperSayHello` çağrılır ve yöntemine bir ad `Blazor`geçirir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="ae85a-138">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="ae85a-139">`CallHelloHelperSayHello`JavaScript işlevini `sayHello` yeni bir örneğiyle çağırır `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="ae85a-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="ae85a-140">*JsInteropClasses/Examplejsınterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="ae85a-141">*Wwwroot/Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="ae85a-142">Ad, `HelloHelper.Name` özelliğini ayarlayan oluşturucuya `HelloHelper`geçirilir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="ae85a-143">JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` JavaScript işlevi tarafından konsola yazılan `Hello, {Name}!` iletiyi döndürür.</span><span class="sxs-lookup"><span data-stu-id="ae85a-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="ae85a-144">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="ae85a-145">Tarayıcının Web geliştirici araçlarında konsol çıkışı:</span><span class="sxs-lookup"><span data-stu-id="ae85a-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="ae85a-146">Bir `DotNetObjectReference`Bellek sızıntısını önlemek ve oluşturan bir bileşende çöp toplamaya izin vermek için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="ae85a-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="ae85a-147">`DotNetObjectReference` Örneği oluşturan sınıftaki nesneyi atma:</span><span class="sxs-lookup"><span data-stu-id="ae85a-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="ae85a-148">`ExampleJsInterop` Sınıfında gösterilen önceki model de bir bileşende uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ae85a-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="ae85a-149">Bileşen veya sınıf `DotNetObjectReference`öğesini atmazsa, şunu çağırarak `.dispose()`istemci üzerindeki nesnesini atın:</span><span class="sxs-lookup"><span data-stu-id="ae85a-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="ae85a-150">Bileşen örneği Yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="ae85a-150">Component instance method call</span></span>

<span data-ttu-id="ae85a-151">Bir bileşenin .NET yöntemlerini çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="ae85a-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="ae85a-152">Bileşene statik `invokeMethod` Yöntem `invokeMethodAsync` çağrısı yapmak için or işlevini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="ae85a-153">Bileşenin static yöntemi, çağırma yöntemi olarak çağrılır `Action`.</span><span class="sxs-lookup"><span data-stu-id="ae85a-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="ae85a-154">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="ae85a-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="ae85a-155">*Pages/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="ae85a-156">Her biri çağrılacak örnek yöntemleri olan birkaç bileşen olduğunda, her bileşenin örnek yöntemlerini (as `Action`) çağırmak için bir yardımcı sınıfı kullanın.</span><span class="sxs-lookup"><span data-stu-id="ae85a-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="ae85a-157">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ae85a-157">In the following example:</span></span>

* <span data-ttu-id="ae85a-158">`JSInterop` Bileşen birkaç `ListItem` bileşen içerir.</span><span class="sxs-lookup"><span data-stu-id="ae85a-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="ae85a-159">Her `ListItem` bileşen bir ileti ve bir düğmeden oluşur.</span><span class="sxs-lookup"><span data-stu-id="ae85a-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="ae85a-160">Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem` `UpdateMessage` Yöntem liste öğesi metnini değiştirir ve düğmeyi gizler.</span><span class="sxs-lookup"><span data-stu-id="ae85a-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="ae85a-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="ae85a-162">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="ae85a-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="ae85a-163">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="ae85a-164">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ae85a-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="ae85a-165">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="ae85a-165">Avoid circular object references</span></span>

<span data-ttu-id="ae85a-166">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="ae85a-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="ae85a-167">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="ae85a-167">.NET method calls.</span></span>
* <span data-ttu-id="ae85a-168">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="ae85a-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="ae85a-169">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="ae85a-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="ae85a-170">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="ae85a-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="ae85a-171">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="ae85a-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="ae85a-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ae85a-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="ae85a-173">InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="ae85a-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="ae85a-174">[Blazor Sunucu uygulamalarında büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="ae85a-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
