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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="8bd23-103">ASP.NET Core'daki JavaScript işlevlerinden .NET yöntemlerini arayınBlazor</span><span class="sxs-lookup"><span data-stu-id="8bd23-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="8bd23-104">Javier [Calvarro Nelson,](https://github.com/javiercn) [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="8bd23-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8bd23-105">Bir Blazor uygulama JavaScript işlevlerinden .NET yöntemlerinden ve .NET yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="8bd23-106">Bu senaryolar *JavaScript birlikte çalışabilirlik* *(JS interop)* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="8bd23-107">Bu makale, JavaScript'ten .NET yöntemlerini çağrıştıran kapsar.</span><span class="sxs-lookup"><span data-stu-id="8bd23-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="8bd23-108">.NET'ten JavaScript işlevlerini nasıl arayacağız hakkında bilgi için bkz. <xref:blazor/call-javascript-from-dotnet></span><span class="sxs-lookup"><span data-stu-id="8bd23-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="8bd23-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8bd23-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="8bd23-110">Statik .NET yöntemi çağrısı</span><span class="sxs-lookup"><span data-stu-id="8bd23-110">Static .NET method call</span></span>

<span data-ttu-id="8bd23-111">JavaScript'ten statik bir .NET yöntemi `DotNet.invokeMethod` `DotNet.invokeMethodAsync` çağırmak için, işlevleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="8bd23-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="8bd23-112">Çağırmak istediğiniz statik yöntemin tanımlayıcısını, işlevi içeren derlemenin adını ve bağımsız değişkenleri geçirin.</span><span class="sxs-lookup"><span data-stu-id="8bd23-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="8bd23-113">Asynchronous sürümü Sunucu senaryoları desteklemek Blazor için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="8bd23-114">.NET yöntemi genel, statik olmalı ve `[JSInvokable]` özniteliğe sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="8bd23-115">Açık genel yöntemleri çağırmak şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="8bd23-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="8bd23-116">Örnek uygulama, bir `int` diziyi döndürmek için bir C# yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="8bd23-117">Öznitelik `JSInvokable` yönteme uygulanır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="8bd23-118">*Sayfa/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8bd23-119">İstemciye sunulan JavaScript C# .NET yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="8bd23-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="8bd23-121">Trigger **.NET statik yöntem ReturnArrayAsync** düğmesi seçildiğinde, konsol çıktısını tarayıcının web geliştirici araçlarında inceleyin.</span><span class="sxs-lookup"><span data-stu-id="8bd23-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="8bd23-122">Konsol çıkışı:</span><span class="sxs-lookup"><span data-stu-id="8bd23-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="8bd23-123">Dördüncü dizi değeri diziye itilir`data.push(4);`( `ReturnArrayAsync`) tarafından döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8bd23-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="8bd23-124">Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak `JSInvokableAttribute` oluşturucuyu kullanarak farklı bir tanımlayıcı belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8bd23-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="8bd23-125">İstemci tarafındaki JavaScript dosyasında:</span><span class="sxs-lookup"><span data-stu-id="8bd23-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="8bd23-126">Örnek yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="8bd23-126">Instance method call</span></span>

<span data-ttu-id="8bd23-127">JavaScript'ten .NET örnek yöntemlerini de arayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd23-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="8bd23-128">JavaScript'ten bir .NET örnek yöntemi çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="8bd23-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="8bd23-129">.NET örneğini JavaScript'e atıfta bulunarak geçirin:</span><span class="sxs-lookup"><span data-stu-id="8bd23-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="8bd23-130">Statik arama `DotNetObjectReference.Create`yapın.</span><span class="sxs-lookup"><span data-stu-id="8bd23-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="8bd23-131">Örneği bir `DotNetObjectReference` örnekte sarın `DotNetObjectReference` ve örneği çağırın. `Create`</span><span class="sxs-lookup"><span data-stu-id="8bd23-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="8bd23-132">`DotNetObjectReference` Nesneleri atın (bu bölümde daha sonra bir örnek görüntülenir).</span><span class="sxs-lookup"><span data-stu-id="8bd23-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="8bd23-133">Instance veya işlevleri kullanarak .NET `invokeMethod` `invokeMethodAsync` örnek yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="8bd23-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="8bd23-134">.NET örneği, JavaScript'ten diğer .NET yöntemlerini çağırırken bir bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="8bd23-135">Örnek uygulama iletileri istemci tarafındaki konsola kaydeder.</span><span class="sxs-lookup"><span data-stu-id="8bd23-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="8bd23-136">Örnek uygulama tarafından gösterilen aşağıdaki örnekler için, tarayıcının konsol çıktısını tarayıcının geliştirici araçlarında inceleyin.</span><span class="sxs-lookup"><span data-stu-id="8bd23-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="8bd23-137">Trigger **.NET örnek yöntemi HelloHelper.SayHello** düğmesi `ExampleJsInterop.CallHelloHelperSayHello` seçildiğinde, çağrılır ve yönteme bir ad `Blazor`geçer.</span><span class="sxs-lookup"><span data-stu-id="8bd23-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="8bd23-138">*Sayfa/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8bd23-139">`CallHelloHelperSayHello`yeni bir örneği `sayHello` ile JavaScript `HelloHelper`işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="8bd23-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="8bd23-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="8bd23-142">Ad, özelliği `HelloHelper`belirleyen ''ın yapıcısına `HelloHelper.Name` geçer.</span><span class="sxs-lookup"><span data-stu-id="8bd23-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="8bd23-143">JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` `Hello, {Name}!` JavaScript işlevi tarafından konsola yazılan iletiyi döndürür.</span><span class="sxs-lookup"><span data-stu-id="8bd23-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="8bd23-144">*JsInteropClasses/ HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="8bd23-145">Tarayıcının web geliştirici araçlarında konsol çıktısı:</span><span class="sxs-lookup"><span data-stu-id="8bd23-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="8bd23-146">Bellek sızıntısını önlemek ve aşağıdaki yaklaşımlardan birini `DotNetObjectReference`benimseyen bir bileşenüzerinde çöp toplamaya izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="8bd23-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="8bd23-147">Örneği oluşturan sınıftaki nesneyi `DotNetObjectReference` atın:</span><span class="sxs-lookup"><span data-stu-id="8bd23-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="8bd23-148">`ExampleJsInterop` Sınıfta gösterilen önceki desen bir bileşende de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="8bd23-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="8bd23-149">Bileşen veya sınıf atmadığı `DotNetObjectReference`zaman, nesneyi istemciye atarak `.dispose()`atın:</span><span class="sxs-lookup"><span data-stu-id="8bd23-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="8bd23-150">Bileşen örneği yöntemi çağrısı</span><span class="sxs-lookup"><span data-stu-id="8bd23-150">Component instance method call</span></span>

<span data-ttu-id="8bd23-151">Bir bileşenin .NET yöntemlerini çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="8bd23-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="8bd23-152">Bileşene `invokeMethod` `invokeMethodAsync` statik bir yöntem çağrısı yapmak için veya işlevi kullanın.</span><span class="sxs-lookup"><span data-stu-id="8bd23-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="8bd23-153">Bileşenin statik yöntemi çağrıyı örnek yöntemine çağrıolarak `Action`sarar.</span><span class="sxs-lookup"><span data-stu-id="8bd23-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="8bd23-154">İstemci tarafındaki JavaScript'te:</span><span class="sxs-lookup"><span data-stu-id="8bd23-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="8bd23-155">*Sayfa/JSInteropComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="8bd23-156">Her biri çağrılması gereken örnek yöntemleri olan birkaç bileşen olduğunda, her `Action`bileşenin örnek yöntemlerini (as s) çağırmak için bir yardımcı sınıf kullanın.</span><span class="sxs-lookup"><span data-stu-id="8bd23-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="8bd23-157">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="8bd23-157">In the following example:</span></span>

* <span data-ttu-id="8bd23-158">Bileşen `JSInterop` birkaç `ListItem` bileşen içerir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="8bd23-159">Her `ListItem` bileşen bir ileti ve düğmeden oluşur.</span><span class="sxs-lookup"><span data-stu-id="8bd23-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="8bd23-160">Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem`''nin `UpdateMessage` yöntemi liste öğesi metnini değiştirir ve düğmeyi gizler.</span><span class="sxs-lookup"><span data-stu-id="8bd23-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="8bd23-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="8bd23-162">İstemci tarafındaki JavaScript'te:</span><span class="sxs-lookup"><span data-stu-id="8bd23-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="8bd23-163">*Paylaşılan/ListItem.razor*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="8bd23-164">*Sayfa/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8bd23-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="8bd23-165">Dairesel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="8bd23-165">Avoid circular object references</span></span>

<span data-ttu-id="8bd23-166">Dairesel başvurular içeren nesneler istemcide aşağıdakiler için serileştirilemez:</span><span class="sxs-lookup"><span data-stu-id="8bd23-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="8bd23-167">.NET yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="8bd23-167">.NET method calls.</span></span>
* <span data-ttu-id="8bd23-168">İade türü dairesel referansları olduğunda JavaScript yöntemi C#'dan çağrı verir.</span><span class="sxs-lookup"><span data-stu-id="8bd23-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="8bd23-169">Daha fazla bilgi için aşağıdaki sorunlara bakın:</span><span class="sxs-lookup"><span data-stu-id="8bd23-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="8bd23-170">Dairesel başvurular desteklenmez, iki tane alın (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="8bd23-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="8bd23-171">Öneri: Serileştirme yaparken dairesel başvuruları işlemek için mekanizma ekleme (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="8bd23-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="8bd23-172">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8bd23-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="8bd23-173">InteropComponent.razor örnek (dotnet/AspNetCore GitHub deposu, 3.1 sürüm şubesi)</span><span class="sxs-lookup"><span data-stu-id="8bd23-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="8bd23-174">[Sunucu uygulamalarında Blazor büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="8bd23-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
