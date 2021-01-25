---
title: ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırın Blazor
author: guardrex
description: Uygulamalarda JavaScript işlevlerinden .NET yöntemlerini çağırmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751629"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="1d6b6-103">ASP.NET Core içindeki JavaScript işlevlerinden .NET yöntemlerini çağırın Blazor</span><span class="sxs-lookup"><span data-stu-id="1d6b6-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="1d6b6-104">, [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d6b6-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d6b6-105">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="1d6b6-106">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="1d6b6-107">Bu makalede, JavaScript 'ten .NET yöntemlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="1d6b6-108">.NET JavaScript işlevlerinin nasıl çağrılacağını öğrenmek için bkz <xref:blazor/call-javascript-from-dotnet> ..</span><span class="sxs-lookup"><span data-stu-id="1d6b6-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="1d6b6-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d6b6-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="1d6b6-110">`<script>` `</body>` `wwwroot/index.html` Dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyadaki ( Blazor Server ) kapanış etiketinden önce js dosyaları (Etiketler) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="1d6b6-111">JS birlikte çalışma yöntemlerine sahip JS dosyalarının Framework JS dosyalarından önce eklendiğinden emin olun Blazor .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="1d6b6-112">Statik .NET yöntemi çağrısı</span><span class="sxs-lookup"><span data-stu-id="1d6b6-112">Static .NET method call</span></span>

<span data-ttu-id="1d6b6-113">JavaScript 'ten statik bir .NET yöntemi çağırmak için `DotNet.invokeMethod` veya `DotNet.invokeMethodAsync` işlevlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-113">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="1d6b6-114">Çağırmak istediğiniz statik metodun tanımlayıcısını, işlevi içeren derlemenin adını ve tüm bağımsız değişkenleri geçirin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-114">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="1d6b6-115">Zaman uyumsuz sürüm, senaryoları desteklemek için tercih edilir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-115">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="1d6b6-116">.NET yöntemi genel, statik ve [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) özniteliği olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-116">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="1d6b6-117">Açık genel yöntemlerin çağrılması Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-117">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="1d6b6-118">Örnek uygulama, bir dizi döndürmek için C# yöntemi içerir `int` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-118">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="1d6b6-119">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)Özniteliği yöntemine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-119">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="1d6b6-120">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-120">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="1d6b6-121">İstemciye sunulan JavaScript, C# .NET yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-121">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="1d6b6-122">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-122">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="1d6b6-123">**`Trigger .NET static method ReturnArrayAsync`** Düğme seçildiğinde, tarayıcının Web geliştirici araçlarında konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-123">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="1d6b6-124">Konsol çıktısı:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-124">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="1d6b6-125">Dördüncü dizi değeri `data.push(4);` tarafından döndürülen diziye () gönderilir `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-125">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="1d6b6-126">Varsayılan olarak, yöntem tanımlayıcısı yöntem adıdır, ancak öznitelik oluşturucusunu kullanarak farklı bir tanımlayıcı belirtebilirsiniz [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) :</span><span class="sxs-lookup"><span data-stu-id="1d6b6-126">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="1d6b6-127">İstemci tarafı JavaScript dosyasında:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-127">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="1d6b6-128">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-128">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="1d6b6-129">Örnek yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="1d6b6-129">Instance method call</span></span>

<span data-ttu-id="1d6b6-130">JavaScript 'ten de .NET örnek yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-130">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="1d6b6-131">JavaScript 'ten bir .NET örnek yöntemi çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-131">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="1d6b6-132">.NET örneğini JavaScript 'e başvuruya göre geçirin:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-132">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="1d6b6-133">İçin statik bir çağrı yapın <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-133">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="1d6b6-134">Örneği bir örnek içinde sarın <xref:Microsoft.JSInterop.DotNetObjectReference> ve <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> örnek üzerinde çağırın <xref:Microsoft.JSInterop.DotNetObjectReference> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-134">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="1d6b6-135">Nesneleri atma <xref:Microsoft.JSInterop.DotNetObjectReference> (Bu bölümün ilerleyen kısımlarında bir örnek görünür).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-135">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="1d6b6-136">Or işlevlerini kullanarak örnekte .NET örnek yöntemlerini çağırın `invokeMethod` `invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-136">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="1d6b6-137">.NET örneği, JavaScript 'ten başka .NET yöntemleri çağrılırken bir bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-137">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="1d6b6-138">Örnek uygulama, iletileri istemci tarafı konsoluna kaydeder.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-138">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="1d6b6-139">Örnek uygulama tarafından gösterilen aşağıdaki örnekler için tarayıcının geliştirici araçlarında tarayıcının konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-139">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="1d6b6-140">**`Trigger .NET instance method HelloHelper.SayHello`** Düğme seçildiğinde, `ExampleJsInterop.CallHelloHelperSayHello` çağrılır ve yöntemine bir ad geçirir `Blazor` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-140">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="1d6b6-141">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-141">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="1d6b6-142">`CallHelloHelperSayHello` JavaScript işlevini `sayHello` Yeni bir örneğiyle çağırır `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-142">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="1d6b6-143">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-143">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="1d6b6-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="1d6b6-145">Ad, `HelloHelper` özelliğini ayarlayan oluşturucuya geçirilir `HelloHelper.Name` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-145">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="1d6b6-146">JavaScript işlevi `sayHello` yürütüldüğünde, `HelloHelper.SayHello` `Hello, {Name}!` JavaScript işlevi tarafından konsola yazılan iletiyi döndürür.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-146">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="1d6b6-147">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-147">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="1d6b6-148">Tarayıcının Web geliştirici araçlarında konsol çıkışı:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-148">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="1d6b6-149">Bir bellek sızıntısını önlemek ve oluşturan bir bileşende çöp toplamaya izin vermek için <xref:Microsoft.JSInterop.DotNetObjectReference> aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-149">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="1d6b6-150">Örneği oluşturan sınıftaki nesneyi atma <xref:Microsoft.JSInterop.DotNetObjectReference> :</span><span class="sxs-lookup"><span data-stu-id="1d6b6-150">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="1d6b6-151">Sınıfında gösterilen önceki model `ExampleJsInterop` de bir bileşende uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-151">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="1d6b6-152">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-152">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="1d6b6-153">Bileşen veya sınıf öğesini atmazsa <xref:Microsoft.JSInterop.DotNetObjectReference> , şunu çağırarak istemci üzerindeki nesnesini atın `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="1d6b6-153">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="1d6b6-154">Bileşen örneği Yöntem çağrısı</span><span class="sxs-lookup"><span data-stu-id="1d6b6-154">Component instance method call</span></span>

<span data-ttu-id="1d6b6-155">Bir bileşenin .NET yöntemlerini çağırmak için:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-155">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="1d6b6-156">`invokeMethod` `invokeMethodAsync` Bileşene statik yöntem çağrısı yapmak için or işlevini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-156">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="1d6b6-157">Bileşenin static yöntemi, çağırma yöntemi olarak çağrılır <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-157">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="1d6b6-158">Blazor ServerBirden çok kullanıcının aynı bileşeni aynı anda kullandığı uygulamalarda, örnek yöntemleri çağırmak için bir yardımcı sınıfı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-158">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="1d6b6-159">Daha fazla bilgi için bkz. [bileşen örnek yöntemi yardımcı sınıfı](#component-instance-method-helper-class) bölümü.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-159">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="1d6b6-160">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-160">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="1d6b6-161">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="1d6b6-162">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-162">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="1d6b6-163">Bağımsız değişkenleri örnek yöntemine geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-163">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="1d6b6-164">JS yöntem çağrısına parametreler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-164">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="1d6b6-165">Aşağıdaki örnekte yöntemine bir ad geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-165">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="1d6b6-166">Gerektiğinde, ek parametreler listeye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-166">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="1d6b6-167">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-167">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="1d6b6-168">Parametreleri için doğru türleri sağlayın <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-168">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="1d6b6-169">C# yöntemlerine parametre listesini sağlayın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-169">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="1d6b6-170"><xref:System.Action>() Parametreleriyle () öğesini çağırın `UpdateMessage` `action.Invoke(name)` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-170">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="1d6b6-171">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-171">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="1d6b6-172">`message` **Çağıran js yöntemi** düğmesi seçildiğinde çıkış:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-172">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="1d6b6-173">Bileşen örnek yöntemi yardımcı sınıfı</span><span class="sxs-lookup"><span data-stu-id="1d6b6-173">Component instance method helper class</span></span>

<span data-ttu-id="1d6b6-174">Yardımcı sınıfı bir örnek yöntemini bir olarak çağırmak için kullanılır <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-174">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="1d6b6-175">Yardımcı sınıflar şu durumlarda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-175">Helper classes are useful when:</span></span>

* <span data-ttu-id="1d6b6-176">Aynı türdeki birçok bileşen aynı sayfada işlenir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-176">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="1d6b6-177">Blazor ServerBirden çok kullanıcının aynı anda bir bileşen kullandığı bir uygulama kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-177">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="1d6b6-178">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-178">In the following example:</span></span>

* <span data-ttu-id="1d6b6-179">`JSInteropExample`Bileşen birkaç bileşen içerir `ListItem` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-179">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="1d6b6-180">Her `ListItem` bileşen bir ileti ve bir düğmeden oluşur.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-180">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="1d6b6-181">Bir `ListItem` bileşen düğmesi seçildiğinde, bu `ListItem` `UpdateMessage` Yöntem liste öğesi metnini değiştirir ve düğmeyi gizler.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-181">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="1d6b6-182">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-182">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="1d6b6-183">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="1d6b6-184">İstemci tarafı JavaScript 'te:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-184">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="1d6b6-185">Yer tutucu, `{APP ASSEMBLY}` uygulamanın uygulama derleme adıdır (örneğin, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-185">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="1d6b6-186">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-186">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

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
        await JS.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="1d6b6-187">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-187">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="1d6b6-188">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="1d6b6-188">Avoid circular object references</span></span>

<span data-ttu-id="1d6b6-189">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-189">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="1d6b6-190">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-190">.NET method calls.</span></span>
* <span data-ttu-id="1d6b6-191">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-191">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="1d6b6-192">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-192">For more information, see the following issues:</span></span>

* [<span data-ttu-id="1d6b6-193">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="1d6b6-193">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="1d6b6-194">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="1d6b6-194">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="1d6b6-195">JS birlikte çalışma çağrılarında boyut sınırları</span><span class="sxs-lookup"><span data-stu-id="1d6b6-195">Size limits on JS interop calls</span></span>

<span data-ttu-id="1d6b6-196">Blazor WebAssembly' De, Framework, JS birlikte çalışma girişleri ve çıkışları için bir sınır vermez.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-196">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="1d6b6-197">Blazor Server' De, JS birlikte çalışabilirlik çağrıları SignalR , tarafından zorlanan hub yöntemleri için izin verilen en büyük gelen ileti boyutuna göre boyut olarak sınırlandırılır <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (varsayılan: 32 KB).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-197">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="1d6b6-198">JS SignalR 'den büyük <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> bir hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-198">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="1d6b6-199">Framework, SignalR hub 'dan istemciye bir ileti boyutuna sınır vermez.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-199">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="1d6b6-200">SignalRGünlüğe kaydetme, [hata ayıklama](xref:Microsoft.Extensions.Logging.LogLevel) veya [izleme](xref:Microsoft.Extensions.Logging.LogLevel)olarak ayarlanmadıysa, yalnızca tarayıcının geliştirici araçları konsolunda bir ileti boyutu hatası görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-200">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="1d6b6-201">Hata: bağlantı bağlantısı kesik ' hata: sunucu kapanmamış bir hata döndürdü: bağlantı bir hata ile kapatıldı. '.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-201">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="1d6b6-202">[ SignalR Sunucu tarafında günlüğe kaydetme](xref:signalr/diagnostics#server-side-logging) , [hata ayıklama](xref:Microsoft.Extensions.Logging.LogLevel) veya [izleme](xref:Microsoft.Extensions.Logging.LogLevel)olarak ayarlandığında, sunucu tarafında günlüğe kaydetme bir <xref:System.IO.InvalidDataException> ileti boyutu hatası için bir yüzey sağlar.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-202">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="1d6b6-203">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-203">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="1d6b6-204">System. ıO. InvalidDataException: 32768B 'nin en büyük ileti boyutu aşıldı.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="1d6b6-205">İleti boyutu Addhuboseçenekler içinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-205">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="1d6b6-206">Sınırı ' de ayarına göre <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> artırın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-206">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1d6b6-207">Aşağıdaki örnek, en fazla alma iletisi boyutunu 64 KB (64 \* 1024) olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-207">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="1d6b6-208">SignalRGelen ileti boyutu sınırının artırılması, daha fazla sunucu kaynağı gerektirme maliyetinden gelir ve sunucuyu kötü niyetli bir kullanıcıdan riskleri artırmak üzere kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-208">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="1d6b6-209">Ayrıca, dizeler veya bayt dizileri olarak bellekte büyük miktarda içeriği okumak, çöp toplayıcıyla kötü olarak çalışan ve ek performans cezaları elde eden ayırmaya neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-209">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="1d6b6-210">Büyük yükleri okumak için bir seçenek, içeriği daha küçük parçalara göndermek ve yükü bir olarak işlemektir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="1d6b6-210">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1d6b6-211">Bu, büyük JSON yüklerini okurken veya veriler JavaScript 'te ham bayt olarak kullanılabilir olduğunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-211">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="1d6b6-212">İçinde, bileşene benzer teknikleri kullanan büyük ikili yükleri göndermeyi gösteren bir örnek için Blazor Server `InputFile` bkz. [ikili gönderme örnek uygulaması](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="1d6b6-212">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="1d6b6-213">JavaScript arasında büyük miktarda veri aktaran kodu geliştirirken aşağıdaki kılavuzu göz önünde bulundurun Blazor :</span><span class="sxs-lookup"><span data-stu-id="1d6b6-213">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="1d6b6-214">Verileri daha küçük parçalara dilimleyin ve tüm veriler sunucu tarafından alınana kadar veri segmentlerini sırayla gönderin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-214">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="1d6b6-215">JavaScript ve C# kodunda büyük nesneler ayırmayın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-215">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="1d6b6-216">Veri gönderirken veya alırken uzun süreler için ana UI iş parçacığını engellemez.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-216">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="1d6b6-217">İşlem tamamlandığında veya iptal edildiğinde tüketilen tüm belleği boşaltın.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-217">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="1d6b6-218">Güvenlik amaçları için aşağıdaki ek gereksinimleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-218">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="1d6b6-219">Geçirilebilen en büyük dosya veya veri boyutunu bildirin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-219">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="1d6b6-220">İstemciden sunucuya en düşük karşıya yükleme hızını bildirin.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-220">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="1d6b6-221">Veriler, sunucu tarafından alındıktan sonra şu olabilir:</span><span class="sxs-lookup"><span data-stu-id="1d6b6-221">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="1d6b6-222">Tüm segmentler toplanana kadar bir bellek arabelleğinde geçici olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-222">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="1d6b6-223">Hemen tüketildi.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-223">Consumed immediately.</span></span> <span data-ttu-id="1d6b6-224">Örneğin, veriler bir veritabanında hemen depolanabilir veya her bir segment alındığında diske yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-224">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="1d6b6-225">JS modülleri</span><span class="sxs-lookup"><span data-stu-id="1d6b6-225">JS modules</span></span>

<span data-ttu-id="1d6b6-226">JS yalıtım için, JS birlikte çalışma, tarayıcıların [EcmaScript modülleri (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) için varsayılan desteğiyle ([ECMAScript belirtimi](https://tc39.es/ecma262/#sec-modules)) birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1d6b6-226">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d6b6-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1d6b6-227">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="1d6b6-228">`InteropComponent.razor` örnek (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="1d6b6-228">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
