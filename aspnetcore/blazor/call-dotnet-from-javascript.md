---
<span data-ttu-id="5af9a-101">title: ' ASP.NET Core ' yazarın JavaScript işlevlerinden .NET yöntemlerini çağır Blazor : Açıklama: ' uygulamalardaki JavaScript işlevlerinden .net yöntemlerini çağırmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="5af9a-101">title: 'Call .NET methods from JavaScript functions in ASP.NET Core Blazor' author: description: 'Learn how to invoke .NET methods from JavaScript functions in Blazor apps.'</span></span>
<span data-ttu-id="5af9a-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="5af9a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5af9a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5af9a-103">'Blazor'</span></span>
- <span data-ttu-id="5af9a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5af9a-104">'Identity'</span></span>
- <span data-ttu-id="5af9a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5af9a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5af9a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5af9a-106">'Razor'</span></span>
- <span data-ttu-id="5af9a-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="5af9a-107">'SignalR' uid:</span></span> 

---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="5af9a-108">ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırınBlazor</span><span class="sxs-lookup"><span data-stu-id="5af9a-108">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="5af9a-109">, [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5af9a-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5af9a-110">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="5af9a-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="5af9a-111">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5af9a-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="5af9a-112">Bu makalede, JavaScript 'ten .NET yöntemlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5af9a-112">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="5af9a-113">.NET JavaScript işlevlerinin nasıl çağrılacağını öğrenmek için bkz <xref:blazor/call-javascript-from-dotnet> ..</span><span class="sxs-lookup"><span data-stu-id="5af9a-113">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="5af9a-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5af9a-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="5af9a-115">Statik .NET yöntemi çağrısı</span><span class="sxs-lookup"><span data-stu-id="5af9a-115">Static .NET method call</span></span>

<span data-ttu-id="5af9a-116">JavaScript 'ten statik bir .NET yöntemi çağırmak için `DotNet.invokeMethod` veya `DotNet.invokeMethodAsync` işlevlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5af9a-116">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="5af9a-117">Çağırmak istediğiniz statik metodun tanımlayıcısını, işlevi içeren derlemenin adını ve tüm bağımsız değişkenleri geçirin.</span><span class="sxs-lookup"><span data-stu-id="5af9a-117">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="5af9a-118">Sunucu senaryolarını desteklemek için zaman uyumsuz sürüm tercih edilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="5af9a-118">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="5af9a-119">.NET yöntemi genel, statik ve `[JSInvokable]` özniteliği olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5af9a-119">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="5af9a-120">Açık genel yöntemlerin çağrılması Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="5af9a-120">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="5af9a-121">Örnek uygulama, bir dizi döndürmek için C# yöntemi içerir `int` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-121">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="5af9a-122">`JSInvokable`Özniteliği yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5af9a-122">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="5af9a-123">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-123">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="5af9a-124">İstemciye sunulan JavaScript, C# .NET yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="5af9a-124">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="5af9a-125">*Wwwroot/Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-125">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="5af9a-126">**Tetikleyici .net static yöntemi ReturnArrayAsync** düğmesi seçildiğinde, tarayıcının Web geliştirici araçlarında konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5af9a-126">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="5af9a-127">Konsol çıktısı:</span><span class="sxs-lookup"><span data-stu-id="5af9a-127">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="5af9a-128">Dördüncü dizi değeri `data.push(4);` tarafından döndürülen diziye () gönderilir `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-128">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="5af9a-129">Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak oluşturucuyu kullanarak farklı bir tanımlayıcı belirtebilirsiniz `JSInvokableAttribute` :</span><span class="sxs-lookup"><span data-stu-id="5af9a-129">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="5af9a-130">İstemci tarafı JavaScript dosyasında:</span><span class="sxs-lookup"><span data-stu-id="5af9a-130">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="5af9a-131">Örnek yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="5af9a-131">Instance method call</span></span>

<span data-ttu-id="5af9a-132">JavaScript 'ten de .NET örnek yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5af9a-132">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="5af9a-133">JavaScript 'ten bir .NET örnek yöntemi çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="5af9a-133">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="5af9a-134">.NET örneğini JavaScript 'e başvuruya göre geçirin:</span><span class="sxs-lookup"><span data-stu-id="5af9a-134">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="5af9a-135">İçin statik bir çağrı yapın `DotNetObjectReference.Create` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-135">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="5af9a-136">Örneği bir örnek içinde sarın `DotNetObjectReference` ve `Create` örnek üzerinde çağırın `DotNetObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-136">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="5af9a-137">Nesneleri atma `DotNetObjectReference` (Bu bölümün ilerleyen kısımlarında bir örnek görünür).</span><span class="sxs-lookup"><span data-stu-id="5af9a-137">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="5af9a-138">Or işlevlerini kullanarak örnekte .NET örnek yöntemlerini çağırın `invokeMethod` `invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-138">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="5af9a-139">.NET örneği, JavaScript 'ten başka .NET yöntemleri çağrılırken bir bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5af9a-139">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="5af9a-140">Örnek uygulama, iletileri istemci tarafı konsoluna kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5af9a-140">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="5af9a-141">Örnek uygulama tarafından gösterilen aşağıdaki örnekler için tarayıcının geliştirici araçlarında tarayıcının konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="5af9a-141">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="5af9a-142">**Tetikleyici .NET örnek yöntemi HelloHelper. SayHello** düğmesi seçildiğinde, `ExampleJsInterop.CallHelloHelperSayHello` çağrılır ve yöntemine bir ad geçirir `Blazor` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-142">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="5af9a-143">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-143">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="5af9a-144">`CallHelloHelperSayHello`JavaScript işlevini `sayHello` Yeni bir örneğiyle çağırır `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-144">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="5af9a-145">*JsInteropClasses/Examplejsınterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-145">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="5af9a-146">*Wwwroot/Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-146">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="5af9a-147">Ad, `HelloHelper` özelliğini ayarlayan oluşturucuya geçirilir `HelloHelper.Name` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-147">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="5af9a-148">JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` `Hello, {Name}!` JavaScript işlevi tarafından konsola yazılan iletiyi döndürür.</span><span class="sxs-lookup"><span data-stu-id="5af9a-148">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="5af9a-149">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-149">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="5af9a-150">Tarayıcının Web geliştirici araçlarında konsol çıkışı:</span><span class="sxs-lookup"><span data-stu-id="5af9a-150">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="5af9a-151">Bir bellek sızıntısını önlemek ve oluşturan bir bileşende çöp toplamaya izin vermek için `DotNetObjectReference` aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="5af9a-151">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="5af9a-152">Örneği oluşturan sınıftaki nesneyi atma `DotNetObjectReference` :</span><span class="sxs-lookup"><span data-stu-id="5af9a-152">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="5af9a-153">Sınıfında gösterilen önceki model `ExampleJsInterop` de bir bileşende uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="5af9a-153">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="5af9a-154">Bileşen veya sınıf öğesini atmazsa `DotNetObjectReference` , şunu çağırarak istemci üzerindeki nesnesini atın `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="5af9a-154">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="5af9a-155">Bileşen örneği Yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="5af9a-155">Component instance method call</span></span>

<span data-ttu-id="5af9a-156">Bir bileşenin .NET yöntemlerini çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="5af9a-156">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="5af9a-157">`invokeMethod` `invokeMethodAsync` Bileşene statik yöntem çağrısı yapmak için or işlevini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5af9a-157">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="5af9a-158">Bileşenin static yöntemi, çağırma yöntemi olarak çağrılır `Action` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-158">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="5af9a-159">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="5af9a-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="5af9a-160">*Pages/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-160">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="5af9a-161">Her biri çağrılacak örnek yöntemleri olan birkaç bileşen olduğunda, her bileşenin örnek yöntemlerini (as) çağırmak için bir yardımcı sınıfı kullanın `Action` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-161">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="5af9a-162">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="5af9a-162">In the following example:</span></span>

* <span data-ttu-id="5af9a-163">`JSInterop`Bileşen birkaç bileşen içerir `ListItem` .</span><span class="sxs-lookup"><span data-stu-id="5af9a-163">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="5af9a-164">Her `ListItem` bileşen bir ileti ve bir düğmeden oluşur.</span><span class="sxs-lookup"><span data-stu-id="5af9a-164">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="5af9a-165">Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem` `UpdateMessage` Yöntem liste öğesi metnini değiştirir ve düğmeyi gizler.</span><span class="sxs-lookup"><span data-stu-id="5af9a-165">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="5af9a-166">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-166">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="5af9a-167">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="5af9a-167">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="5af9a-168">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-168">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="5af9a-169">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5af9a-169">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="5af9a-170">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="5af9a-170">Avoid circular object references</span></span>

<span data-ttu-id="5af9a-171">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="5af9a-171">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="5af9a-172">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="5af9a-172">.NET method calls.</span></span>
* <span data-ttu-id="5af9a-173">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="5af9a-173">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="5af9a-174">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="5af9a-174">For more information, see the following issues:</span></span>

* [<span data-ttu-id="5af9a-175">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="5af9a-175">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="5af9a-176">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="5af9a-176">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="5af9a-177">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5af9a-177">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="5af9a-178">InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="5af9a-178">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="5af9a-179">[Sunucu uygulamalarında büyük veri aktarımları gerçekleştirin Blazor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="5af9a-179">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
