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
ms.openlocfilehash: 064f504e94cd65862370d4551c6cb44210a8238f
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967304"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="93c7e-103">ASP.NET Core .NET metotlarından JavaScript işlevlerini çağırınBlazor</span><span class="sxs-lookup"><span data-stu-id="93c7e-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="93c7e-104">Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="93c7e-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="93c7e-105">Bir Blazor uygulama, JavaScript işlevlerinden .net yöntemleri ve .net yöntemlerinden JavaScript işlevlerini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="93c7e-106">Bu senaryolar *JavaScript birlikte çalışabilirliği* (*js birlikte çalışma*) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="93c7e-107">Bu makalede, .NET 'ten JavaScript işlevlerini çağırma ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="93c7e-108">JavaScript 'ten .NET yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:blazor/call-dotnet-from-javascript>..</span><span class="sxs-lookup"><span data-stu-id="93c7e-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="93c7e-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93c7e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="93c7e-110">.NET 'ten JavaScript 'e çağrı yapmak için `IJSRuntime` soyutlamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="93c7e-111">JS birlikte çalışma çağrıları vermek için, bu `IJSRuntime` soyutlamayı bileşeninizdeki ekler.</span><span class="sxs-lookup"><span data-stu-id="93c7e-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="93c7e-112">Yöntemi `InvokeAsync<T>` , herhangi BIR sayıda JSON seri hale getirilebilir bağımsız değişkenle birlikte çağırmak istediğiniz JavaScript işlevi için bir tanımlayıcı alır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="93c7e-113">İşlev tanımlayıcısı genel kapsama (`window`) göredir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="93c7e-114">Çağırmak `window.someScope.someFunction`isterseniz, tanımlayıcı olur `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="93c7e-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="93c7e-115">Çağrılmadan önce işlevi kaydetmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="93c7e-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="93c7e-116">Dönüş türünün `T` de JSON seri hale getirilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="93c7e-117">`T`döndürülen JSON türüyle en iyi eşleşen .NET türüyle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="93c7e-118">Prerendering Blazor özellikli sunucu uygulamaları için, ilk prerendering sırasında JavaScript 'e çağrı yapılamaz.</span><span class="sxs-lookup"><span data-stu-id="93c7e-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="93c7e-119">JavaScript birlikte çalışma çağrılarının, tarayıcıyla bağlantı kurulana kadar ertelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="93c7e-120">Daha fazla bilgi için bkz. [ Blazor sunucu uygulamasının ne zaman prerendering olduğunu Algıla](#detect-when-a-blazor-server-app-is-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="93c7e-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="93c7e-121">Aşağıdaki örnek, JavaScript tabanlı bir kod çözücü olan [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)tabanlıdır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="93c7e-122">Örnek, C# yönteminden bir JavaScript işlevinin nasıl çağrılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="93c7e-123">JavaScript işlevi bir C# yönteminden bir bayt dizisi kabul eder, dizinin kodunu çözer ve görüntülenecek metni bileşene döndürür.</span><span class="sxs-lookup"><span data-stu-id="93c7e-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="93c7e-124">*Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde, geçirilen bir dizinin kodunu çözmek için kullanılan `TextDecoder` bir JavaScript işlevi sağlayın ve kodu çözülen değeri döndürün:</span><span class="sxs-lookup"><span data-stu-id="93c7e-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="93c7e-125">Önceki örnekte gösterilen kod gibi JavaScript kodu, betik dosyasına yönelik bir başvuruya sahip bir JavaScript dosyasından (*. js*) de yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="93c7e-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="93c7e-126">Aşağıdaki bileşen:</span><span class="sxs-lookup"><span data-stu-id="93c7e-126">The following component:</span></span>

* <span data-ttu-id="93c7e-127">Bir bileşen `convertArray` düğmesi ( `JSRuntime` **diziyi Dönüştür**) seçildiğinde JavaScript işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="93c7e-128">JavaScript işlevi çağrıldıktan sonra, geçirilen dizi bir dizeye dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="93c7e-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="93c7e-129">Dize, görüntüleme için bileşene döndürülür.</span><span class="sxs-lookup"><span data-stu-id="93c7e-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="93c7e-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="93c7e-130">IJSRuntime</span></span>

<span data-ttu-id="93c7e-131">`IJSRuntime` Soyutlamayı kullanmak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="93c7e-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="93c7e-132">`IJSRuntime` Özet Razor bileşene (*. Razor*) ekleme:</span><span class="sxs-lookup"><span data-stu-id="93c7e-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="93c7e-133">*Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde bir `handleTickerChanged` JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="93c7e-134">İşlevi ile `IJSRuntime.InvokeVoidAsync` çağrılır ve bir değer döndürmez:</span><span class="sxs-lookup"><span data-stu-id="93c7e-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="93c7e-135">`IJSRuntime` Soyutlamayı bir sınıfa (*. cs*) Ekle:</span><span class="sxs-lookup"><span data-stu-id="93c7e-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="93c7e-136">*Wwwroot/index.html* Blazor ( `<head>` webassembly) veya *Pages/_Host. cshtml* (Blazor Server) öğesinin içinde bir `handleTickerChanged` JavaScript işlevi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="93c7e-137">İşlevi ile `JSRuntime.InvokeAsync` çağrılır ve bir değer döndürür:</span><span class="sxs-lookup"><span data-stu-id="93c7e-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="93c7e-138">[Buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)ile dinamik içerik oluşturma için `[Inject]` özniteliğini kullanın:</span><span class="sxs-lookup"><span data-stu-id="93c7e-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="93c7e-139">Bu konuya eşlik eden istemci tarafı örnek uygulamada, Kullanıcı girişi almak ve bir hoş geldiniz iletisi göstermek üzere DOM ile etkileşime geçen uygulama için iki JavaScript işlevi mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="93c7e-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="93c7e-140">`showPrompt`&ndash; Kullanıcı girişini kabul etmek için bir istem üretir (kullanıcının adı) ve arayan adına adı döndürür.</span><span class="sxs-lookup"><span data-stu-id="93c7e-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="93c7e-141">`displayWelcome`&ndash; ' A SAHIP `id` bir DOM nesnesine çağırandan bir hoş geldiniz iletisi atar `welcome`.</span><span class="sxs-lookup"><span data-stu-id="93c7e-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="93c7e-142">*Wwwroot/Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="93c7e-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="93c7e-143">JavaScript dosyasına `<script>` başvuran etiketi *Wwwroot/index.html* File (Blazor webassembly) veya *Pages/_Host. cshtml* dosyası (Blazor sunucu) içine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="93c7e-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="93c7e-144">*Wwwroot/index.html* (Blazor webassembly):</span><span class="sxs-lookup"><span data-stu-id="93c7e-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="93c7e-145">*Pages/_Host. cshtml* (Blazor sunucu):</span><span class="sxs-lookup"><span data-stu-id="93c7e-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="93c7e-146">Etiket dinamik olarak `<script>` güncelleştirilemediğinden bir bileşen dosyasına etiket yerleştirmeyin. `<script>`</span><span class="sxs-lookup"><span data-stu-id="93c7e-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="93c7e-147">.NET yöntemleri, çağırarak `IJSRuntime.InvokeAsync<T>`, *Examplejsınterop. js* dosyasındaki JavaScript işlevleriyle birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="93c7e-148">Soyutlama `IJSRuntime` , Blazor sunucu senaryolarına izin vermek için zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="93c7e-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="93c7e-149">Uygulama bir Blazor webassembly uygulaması ise ve bir JavaScript işlevini eşzamanlı olarak çağırmak istiyorsanız bunun yerine alt türe çevirme yapın `IJSInProcessRuntime` ve çağırın `Invoke<T>` .</span><span class="sxs-lookup"><span data-stu-id="93c7e-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="93c7e-150">Çoğu JS birlikte çalışma kitaplıklarının, kitaplıkların tüm senaryolarda kullanılabilir olmasını sağlamak için zaman uyumsuz API 'Leri kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="93c7e-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="93c7e-151">Örnek uygulama, JS birlikte çalışabilirliği göstermek için bir bileşeni içerir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="93c7e-152">Bileşen:</span><span class="sxs-lookup"><span data-stu-id="93c7e-152">The component:</span></span>

* <span data-ttu-id="93c7e-153">Bir JavaScript istemi aracılığıyla Kullanıcı girişini alır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="93c7e-154">İşlemek için bileşene metni döndürür.</span><span class="sxs-lookup"><span data-stu-id="93c7e-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="93c7e-155">Bir hoş geldiniz iletisini göstermek için DOM ile etkileşime sahip ikinci bir JavaScript işlevini çağırır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="93c7e-156">*Pages/Jsınterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="93c7e-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="93c7e-157">`TriggerJsPrompt` Bileşenin **tetikleme JavaScript istem** düğmesi seçilerek çalıştırıldığında, `showPrompt` *Wwwroot/examplejsınterop. js* dosyasında verilen JavaScript işlevi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="93c7e-158">`showPrompt` IşLEVI, HTML kodlu ve bileşene döndürülen kullanıcı girişini (kullanıcının adı) kabul eder.</span><span class="sxs-lookup"><span data-stu-id="93c7e-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="93c7e-159">Bileşen, kullanıcının adını yerel bir değişkende depolar `name`.</span><span class="sxs-lookup"><span data-stu-id="93c7e-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="93c7e-160">İçinde `name` depolanan dize, hoş geldiniz iletisini bir başlık etiketine Işleyen bir JavaScript işlevine `displayWelcome`iletilen bir hoş geldiniz iletisine eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="93c7e-161">Void JavaScript işlevini çağırın</span><span class="sxs-lookup"><span data-stu-id="93c7e-161">Call a void JavaScript function</span></span>

<span data-ttu-id="93c7e-162">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) veya [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) döndüren JavaScript işlevleri ile `IJSRuntime.InvokeVoidAsync`çağırılır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="93c7e-163">Blazor Sunucu uygulamasının ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="93c7e-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="93c7e-164">Öğelere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="93c7e-164">Capture references to elements</span></span>

<span data-ttu-id="93c7e-165">Bazı JS birlikte çalışma senaryoları HTML öğelerine başvurular gerektirir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="93c7e-166">Örneğin, bir kullanıcı arabirimi kitaplığı başlatma için bir öğe başvurusu gerektirebilir veya `focus` ya `play`da gibi bir öğe üzerinde komut benzeri API 'ler çağırmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="93c7e-167">Aşağıdaki yaklaşımı kullanarak bir bileşen içindeki HTML öğelerine başvuruları yakalayın:</span><span class="sxs-lookup"><span data-stu-id="93c7e-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="93c7e-168">HTML öğesine `@ref` bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="93c7e-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="93c7e-169">Adı `@ref` özniteliğin değeriyle eşleşen bir `ElementReference` tür alanı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="93c7e-170">Aşağıdaki örnek, `username` `<input>` öğesine bir başvuru yakalama göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="93c7e-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="93c7e-171">Yalnızca ile Blazoretkileşimde bulunmayan boş bir öğenin içeriğini bulunmamalıdır için bir öğe başvurusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="93c7e-172">Bu senaryo, bir 3. taraf API 'SI öğeye içerik sağladığı zaman yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="93c7e-173">Öğesiyle Blazor etkileşmediği için, öğe ve Dom gösterimi arasında Blazorbir çakışma olabilir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="93c7e-174">Aşağıdaki örnekte, bu öğenin liste öğelerini (`<li>`) doldurmak üzere Dom ile etkileşimde bulunduğundan, sıralanmamış listenin`ul`() Blazor içeriğini zaman içinde () () zaman *zaman aşmaktır* .</span><span class="sxs-lookup"><span data-stu-id="93c7e-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="93c7e-175">JS birlikte çalışma öğesi içeriğini değiştiriyorsa `MyList` ve Blazor öğe için SLA 'lar uygulamaya ÇALıŞıRSA, diffler Dom ile eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="93c7e-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="93c7e-176">.NET kodu açısından düşünüldüğünde, donuk bir `ElementReference` tanıtıcıdır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="93c7e-177">İle *only* `ElementReference` YAPABILECEĞINIZ tek şey, JS birlikte çalışma yoluyla JavaScript koduna geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="93c7e-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="93c7e-178">Bunu yaptığınızda, JavaScript tarafı kodu normal DOM API 'Leri ile kullanılabilecek `HTMLElement` bir örnek alır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="93c7e-179">Örneğin, aşağıdaki kod bir öğe üzerinde odağı ayarlamaya izin veren bir .NET genişletme yöntemi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="93c7e-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="93c7e-180">*Examplejsınterop. js*:</span><span class="sxs-lookup"><span data-stu-id="93c7e-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="93c7e-181">Değer döndürmeyen bir JavaScript işlevini çağırmak için kullanın `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="93c7e-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="93c7e-182">Aşağıdaki kod, yakalanmış `ElementReference`olan önceki JavaScript işlevini çağırarak Kullanıcı adı girişi üzerinde odağı ayarlar:</span><span class="sxs-lookup"><span data-stu-id="93c7e-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="93c7e-183">Bir genişletme yöntemi kullanmak için, `IJSRuntime` örneği alan bir statik genişletme yöntemi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="93c7e-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="93c7e-184">`Focus` Yöntemi doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="93c7e-185">Aşağıdaki örnek, `Focus` yöntemin `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="93c7e-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="93c7e-186">`username` Değişken yalnızca bileşen işlendikten sonra doldurulur.</span><span class="sxs-lookup"><span data-stu-id="93c7e-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="93c7e-187">Doldurulmamış bir `ElementReference` JavaScript koduna geçirilirse JavaScript kodu bir değeri alır `null`.</span><span class="sxs-lookup"><span data-stu-id="93c7e-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="93c7e-188">Bileşen işlemeyi tamamladıktan sonra öğe başvurularını değiştirmek için (bir öğe üzerinde ilk odağı ayarlamak için) [Onafterrenderasync veya OnAfterRender bileşen yaşam döngüsü yöntemlerini](xref:blazor/lifecycle#after-component-render)kullanın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="93c7e-189">Genel türlerle çalışırken ve bir değer döndürürken, [Valuetask\<T>](xref:System.Threading.Tasks.ValueTask`1)kullanın:</span><span class="sxs-lookup"><span data-stu-id="93c7e-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="93c7e-190">`GenericMethod`, bir türü olan doğrudan nesnesi üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="93c7e-191">Aşağıdaki örnek, `GenericMethod` `JsInteropClasses` ad alanından kullanılabilir olduğunu varsayar:</span><span class="sxs-lookup"><span data-stu-id="93c7e-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="93c7e-192">Bileşenler arasında başvuru öğeleri</span><span class="sxs-lookup"><span data-stu-id="93c7e-192">Reference elements across components</span></span>

<span data-ttu-id="93c7e-193">Yalnızca `ElementReference` bir bileşen `OnAfterRender` yönteminde (ve bir öğe başvurusu bir `struct`) garanti edilir, bu nedenle bileşenler arasında bir öğe başvurusu geçirilememelidir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="93c7e-194">Bir üst bileşen için bir öğe başvurusunu diğer bileşenlere kullanılabilir hale getirmek için, üst bileşen şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="93c7e-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="93c7e-195">Alt bileşenlerin geri çağırmaları kaydetmesine izin ver.</span><span class="sxs-lookup"><span data-stu-id="93c7e-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="93c7e-196">Geçirilen öğe başvurusuyla `OnAfterRender` olay sırasında kayıtlı geri çağırmaları çağırın.</span><span class="sxs-lookup"><span data-stu-id="93c7e-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="93c7e-197">Bu yaklaşım dolaylı olarak, alt bileşenlerin üst öğenin öğe başvurusuyla etkileşime geçmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="93c7e-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="93c7e-198">Aşağıdaki Blazor webassembly örneğinde yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="93c7e-199">`<head>` *Wwwroot/index.html*' de:</span><span class="sxs-lookup"><span data-stu-id="93c7e-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="93c7e-200">`<body>` *Wwwroot/index.html*' de:</span><span class="sxs-lookup"><span data-stu-id="93c7e-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="93c7e-201">*Pages/Index. Razor* (üst bileşen):</span><span class="sxs-lookup"><span data-stu-id="93c7e-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="93c7e-202">*Sayfa/dizin. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="93c7e-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
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

<span data-ttu-id="93c7e-203">*Shared/gözetimi ve Prompt. Razor* (alt bileşen):</span><span class="sxs-lookup"><span data-stu-id="93c7e-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="93c7e-204">*Shared/gözetimi ve Prompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="93c7e-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="93c7e-205">Harden JS birlikte çalışma çağrıları</span><span class="sxs-lookup"><span data-stu-id="93c7e-205">Harden JS interop calls</span></span>

<span data-ttu-id="93c7e-206">JS birlikte çalışması, ağ hataları nedeniyle başarısız olabilir ve güvenilmez olarak değerlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="93c7e-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="93c7e-207">Varsayılan olarak, bir Blazor sunucu uygulaması, bir dakika sonra sunucu üzerinde BIR kez js birlikte çalışabilirlik çağrısı çağırır.</span><span class="sxs-lookup"><span data-stu-id="93c7e-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="93c7e-208">Bir uygulama, 10 saniye gibi daha agresif zaman aşımına uğrayedebilmesine, aşağıdaki yaklaşımlardan birini kullanarak zaman aşımını ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="93c7e-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="93c7e-209">İçinde `Startup.ConfigureServices`genel olarak, zaman aşımını belirtin:</span><span class="sxs-lookup"><span data-stu-id="93c7e-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="93c7e-210">Bileşen kodunda çağrı başına, tek bir çağrı zaman aşımını belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="93c7e-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="93c7e-211">Kaynak tükenmesi hakkında daha fazla bilgi için bkz <xref:security/blazor/server/threat-mitigation>..</span><span class="sxs-lookup"><span data-stu-id="93c7e-211">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="93c7e-212">Döngüsel nesne başvurularından kaçının</span><span class="sxs-lookup"><span data-stu-id="93c7e-212">Avoid circular object references</span></span>

<span data-ttu-id="93c7e-213">Döngüsel başvurular içeren nesneler, her biri için istemcide serileştirilemiyor:</span><span class="sxs-lookup"><span data-stu-id="93c7e-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="93c7e-214">.NET yöntemi çağrıları.</span><span class="sxs-lookup"><span data-stu-id="93c7e-214">.NET method calls.</span></span>
* <span data-ttu-id="93c7e-215">Dönüş türünün döngüsel başvuruları olduğunda C# ' den JavaScript Yöntem çağrıları.</span><span class="sxs-lookup"><span data-stu-id="93c7e-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="93c7e-216">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="93c7e-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="93c7e-217">Döngüsel başvurular desteklenmez, iki alma (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="93c7e-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="93c7e-218">Teklif: serileştirilirken döngüsel başvuruları işlemek için mekanizma ekleyin (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="93c7e-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="93c7e-219">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="93c7e-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="93c7e-220">InteropComponent. Razor örneği (DotNet/AspNetCore GitHub deposu, 3,1 yayın dalı)</span><span class="sxs-lookup"><span data-stu-id="93c7e-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="93c7e-221">[Blazor Sunucu uygulamalarında büyük veri aktarımları gerçekleştirin](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="93c7e-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
